# Daemon

프로그램은 하드디스크에서 저장된 일련의 명령 집합으로서 실행 가능한 파일을 뜻한다. 프로그램을 실행하게 되면은 메모리에 로딩되는데 실행되는 프로그램을 프로세스라 불린다. 프로세스는 foreground 혹은 background 상태에서 실행된다. Foreground 상태는 화면이나 shell에서 실행 및 입출력 이루어지고 입출력 작업이 필요하지 않은 경우 background 상태로 작업을 수행할 수 있다.

데몬은 background 상태에서 특정 서비스를 위해 지속적으로 실행되는 서버 프로세스이다. 데몬은 각각의 서비스가 사용하는 port를 관리하며 일반적인 서비스에 존재한다. 이는 부팅 중에 메모리에 로딩되어 컴퓨가 종료될 때까지 유지된다. 일반적인 프로세스는 실행 상태에서 주어진 일련의 명령을 수행하면 소멸되는 반면 <u>데몬은 일련의 명령을 수행 완료한 후에도 메모리에 남아 특정한 조건이 성립되면 다시 명령을 수행한다.</u>

데몬은 두 가지 실행 모드가 존재한다. 첫 번째는 standalone 모드로서 독립적으로 실행되고 항상 메모리에 상주하여 서비스 요청이 들어오면 바로 응답을 한다. 두 번째는 xinetd 모드인데 이는 슈퍼 데몬에 의해 관리되며 서비스 요청이 있을 경우에만 메모리에 적재되어 응답을 한다. 필요할 경우에만 메모리에 할당되기 때문에 서버 부하를 줄일 수 있다는 장점이 있는 반면 standalone에 비해서 느리다는 단점이 존재한다. 그러므로 빠른 응답 속도가 필요하지 않을 경우 이를 사용한다.



### Running multiple processes on a single daemon

하나의 데몬에서 여러 프로세스를 실행하는 방법은 각 프로세스가 특정 시차적 간격을 두는 것이다. 이는 파이쎤에서 timeloop란 라이브러리를 사용해 각 함수마다 시차적 간격을 두고 각각 다른 스레드에서 실행시켜 준다.

```
$ pip install timeloop
```

```python
# 예제

import time
from timeloop import Timeloop
from datetime import timedelta

tl = Timeloop()

@tl.job(interval=timedelta(seconds=2))
def sample_job_every_2s():
    print "2s job current time : {}".format(time.ctime())
    
@tl.job(interval=timedelta(seconds=5))
def sample_job_every_5s():
    print "5s job current time : {}".format(time.ctime())
    
@tl.job(interval=timedelta(seconds=10))
def sample_job_every_10s():
    print "10s job current time : {}".format(time.ctime())
    
if __name__ == "__main__":
    tl.start(block=True)
    
#Output:
#2s job current time : Tue Oct 16 17:55:35 2018
#2s job current time : Tue Oct 16 17:55:37 2018
#5s job current time : Tue Oct 16 17:55:38 2018
#2s job current time : Tue Oct 16 17:55:39 2018
#2s job current time : Tue Oct 16 17:55:41 2018
#10s job current time : Tue Oct 16 17:55:43 2018
```



### Reference

[https://poppy-leni.tistory.com/entry/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%8D%B0%EB%AA%AC%EC%9D%B4%EB%9E%80](https://poppy-leni.tistory.com/entry/프로그램-프로세스-데몬이란)

https://titanic1997.tistory.com/20

https://medium.com/greedygame-engineering/an-elegant-way-to-run-periodic-tasks-in-python-61b7c477b679