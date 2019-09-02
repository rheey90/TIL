# Project Git Flow

프로젝트에서 git을 사용할 경우 upstream, origin, local 수준으로 나눠 진행된다. upstream은 기존 레포이며 포크를 하여 자신의 레포 즉 origin으로 가져와 local 저장소에 클론을 한다. local로 클론을 한 후, master 브랜치에서 dev란 브랜치를 만들어야 한다.

```
$ git checkout -b dev
```

dev 브랜치에서 개발과 관련된 모든 작업이 일어난다. master 브랜치로 push하는 경우가 없도록 주의하여야 한다. 프로젝트의 기능 혹은 컴포넌트마다 dev 브랜치에서 추가적으로 관련 브랜치를 생성하여 작업할 수 있다.

```
$ git checkout -b feature1
$ git add feature1
$ git commit -m "Completed feature1"
$ git push origin feature1
```

예를 들어 feature1을 만들 경우 dev 브랜치에서 해당 브랜치를 생성하여 파일을 추가하여 거기에 push를 할 수 있다.

```
$ git remote add upstream <upstream 레포 주소>
```

그러고 나서 origin의 feature1 브랜치에 있는 파일들을 upstream 레포로 pull request를 요청하기 위해 upstream를 remote으로 추가해준다. Pull request를 요청한 후 upstream 레포로 가서 해당 pull request를 클릭한 뒤 upstream dev 브랜치로 merge할 수 있다. 단, 팀 내 코드 리뷰 없이 merge를 하는 것은 금지사항이기 때문에 반드시 코드 리뷰를 진행한 후 merge를 해야한다. Merge가 완료된 후 upstream의 dev 브랜치의 코드를 자신의 local dev 브랜치로 pull하여 local의 dev 브랜치를 최신화한다. 최신화가 완료된 후, dev 브랜치의 feature 브랜치를 생성하여 계속 작업해 나가면 된다. 모든 작업이 완료가 되었으면 upstream의 dev 브랜치를 master 브랜치에 적용하여 version 1을 만들 수 있다.

![스크린샷 2019-09-02 오후 10.53.49](/Users/damagedcase/Desktop/스크린샷 2019-09-02 오후 10.53.49.png)