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
