#4주 프로젝트 10일차 (2019/09/26)

오늘은 기업 측 CTO님과 회의 시간을 가져 여러 이야기를 나눴고 그 중 중요했던 내용 하나는 여러 데몬 프로세스를 돌려 동시에 여러 유저의 정보와 동영상 정보를 가져오는 것이었다.



### Threading vs Multiprocessing

먼저 데몬 프로세스에 대해서 조사하던 중 쓰레딩과 멀티 프로세싱을 다루는 글을 봤다. 이 글에 따르면 어떤 작업을 수행하는지에 따라서 쓰레딩 혹은 멀티 프로세싱을 써야된다고 한다. 먼저 파이썬은 linear한 언어이므로 쓰레딩으로 parallel CPU 연산하기에는 불리하지만 약간의 프로세싱 파워가 더 필요하고 프로세서가 유휴 상태에서 데이터가 들어오실 기다릴 때 웹 크롤링 같은 작업 수행 시, 쓰레딩이 더 유리하다고 한다. 특히, 웹 크콜링과 같은 I/O 스크립트는 외부 소스로부터 데이터가 들어오길 기다리는 시간이 많기 때문에 여러 웹 사이트로부터 데이터를 받을 시 동시에 받고 합칠 수 있어 쓰레딩이 적합하다. 반대로 멀티 프로세싱은 CPU를 상당히 필요로 하는 작업에 더 적합하다.



### Python Daemon Thread

파이썬으로 데몬을 실행하려면 threading을 import해서 사용해야 한다.

```python
import threading
import time


def print_work_a():
    print('Starting of thread:', threading.current_thread().name)
    print('Finishing of thread:', threading.current_thread().name)


def print_work_b():
    print('Starting of thread:', threading.current_thread().name)
    time.sleep(3) # 3초 후에 아래 내용이 출력됨
    print('Finishing of thread:', threading.current_thread().name)


a = threading.Thread(target=print_work_a, name='Thread-a')
b = threading.Thread(target=print_work_b, name='Thread-b')

a.start()
b.start()
```

위에 코드를 출력하면 아래와 같은 결과가 출력된다.

Starting of thread: Thread-a
Finishing of thread: Thread-a
Starting of thread: Thread-b
Finishing of thread: Thread-b (3초 후에 나타남)

```python
import threading
import time


def print_work_a():
    print('Starting of thread:', threading.current_thread().name)
    print('Finishing of thread:', threading.current_thread().name)


def print_work_b():
    print('Starting of thread:', threading.current_thread().name)
    time.sleep(3)
    print('Finishing of thread:', threading.current_thread().name)


a = threading.Thread(target=print_work_a, name='Thread-a')
b = threading.Thread(target=print_work_b, name='Thread-b', daemon=True)

a.start()
b.start()
```

그러나 b에 daemon=True라는 매개변수를 추가해주면

Starting of thread: Thread-a
Finishing of thread: Thread-a
Starting of thread: Thread-b

b는 끝나지 않은채로 프로그램이 종료되는데 이는 데몬 쓰레드가 되었기 때문이다.



### Reference

https://www.journaldev.com/16152/python-daemon-thread

https://timber.io/blog/multiprocessing-vs-multithreading-in-python-what-you-need-to-know/

