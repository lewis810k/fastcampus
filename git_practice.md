# git 실습

### ADD

![add_1](https://s30.postimg.org/6nvybpcy9/add_1.png)   
- *working directory* 내에서 변경사항이 발생할 경우 `git`이 해당 변경사항을 모니터링한다.   
- `git status`를 입력하면 *stage*로 올릴 수 있는(`add` 가능한) 파일 목록을 보여준다.  

![add_2](https://s30.postimg.org/ayam7ai1d/add_2.png)   
- `git add index.html` - **index.html** 하나의 파일만 `add` 한다.     
- `git add -A` - 전체 변경사항을 모두 `add` 한다.

-

### COMMIT

![commit_1](https://s28.postimg.org/ma239ujyl/commit_1.png)  
- `add`된 내용을 `git commit`으로 *local repository*에 등록한다.   
- 초기설정 시 git 메일주소와 이름을 설정한다.   

![commit_2](https://s28.postimg.org/976gqktql/commit_2.png)   
- “첫번째 index.html 커밋입니다” 라고 commit message를 입력한 화면    
- commit 내용을 저장하기 위해 ctrl + x를 입력   

![commit_3](https://s28.postimg.org/cffjhd5e5/commit_3.png)   
- Y 입력   

![commit_4](https://s28.postimg.org/jwoqwkux9/commit_4.png)   
- 이름을 특별히 변경하지 않을 경우 여기서 `enter`를 누르면 `commit`이 완료된다.   
- 한 줄의 명령어로 `commit`할 수 있다. -m은 master를 의미    

> git commit -m “제목 내용 변경”  

- 한 줄의 명령어로 `add`와 `commit`을 동시에 할 수 있다.   

> git commit -a -m “한 번에 커밋하기”   

![commit_5](https://s24.postimg.org/tgjdp7set/commit_5.png)   
- git log 내용: 누가 언제 어떤 이유에서 해당 커밋을 진행했는지 내용 출력   
- 16진수로 표시된 **commit number**를 통해 `merge`, `revert`, `reset` 등을 진행   

![commit_6](https://s24.postimg.org/qnq656s2d/commit_6.png)   
- `add` 되었던 내용을 다시 *stage*로 내릴 수 있다.    

-

### RESET, REVERT   

`reset`이나 `revert`를 사용할 경우 꼬이거나 추적하기 힘들어지는 경우가 많아지기 때문에 문제가 발생한 부분을 새로 수정해서 `commit`하는 경우가 더 많다.  

-

### gitignore

git에서 관리가 되지 않아도 될 파일들의 목록을 저장하는 파일   
목록에 있는 파일들은 변경사항을 추적하지 않아 stage로 올라가지 않도록 해준다.   
![commit_7](https://s27.postimg.org/5xydg8g7n/commit_7.png)   
- 우리가 쓸 것으로 예상되는 모든 tool과 system을 입력한다.   
- generate를 누르면 각 항목에서 제외되어야 목록이 생성된다.   
- 생성된 내용들을 .gitignore 파일에 추가한다.  

> 주의사항 : 이 작업은 반드시 git init(local repository 생성) 하자마자 해야 한다. 컴퓨터 환경에 따라 쓰레기 파일들을 생성하기 때문에 .gitignore에서 처리해 주지 않고 다른 사람의 작업을 pull하면 conflict가 발생한다.  이 사항만 지켜도 불필요한 conflict를 미연에 방지할 수 있다.   

-

### CLONE

![commit_8](https://s27.postimg.org/5waxec077/commit_8.png)   
- git 디렉토리가 **`clone`될 디렉토리로 이동**해서 `git clone [address]`을 입력한다.   

