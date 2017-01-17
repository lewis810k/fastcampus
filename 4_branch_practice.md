### Branch 실습
실습 환경 구축   
: working directory 생성 → git init → .gitignore 추가 → initial commit   
: initial commit 후의 상황에서 실습 진행  

![branch_1](https://s24.postimg.org/qa44iz445/branch_1.png)

- css, html branch를 생성한다.  
> `git branch html`: html이라는 브랜치 생성  
> `git branch` : 브랜치 목록을 보여준다  

![branch_2](https://s24.postimg.org/6gs0q9qqd/branch_2.png)

- `git checkout`명령어로 다른 브랜치로 이동 가능   

![branch_3](https://s24.postimg.org/8mmbkru6t/branch_3.png)

- html의 하위 브랜치로 사용할 feature 브랜치를 생성한다.  
( git branch -d branch-name으로 브랜치 삭제 가능)


> `commit`을 하지 않고 `checkout`하게 되면 브랜치가 이동할 때 변경내용을 그대로 가지고 이동하기 때문에 `checkout`하기 전에 `commit`을 해야 한다.   
> sub branch의 내용을 main branch로 merge할 때 sub branch가 사라지는 것이 아니라 내용만 **반영**한다. sub branch에서는 새로운 작업을 진행할 수 있다.

머지 내용 추가 예정

![branch_4](https://s24.postimg.org/6jbwd3udx/branch_4.png)  

![branch_5](https://s24.postimg.org/jorej7o9h/branch_5.png)
