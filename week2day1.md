# git

> git은 버전관리시스템(VCS)이다.
  
-

### VCS란?

> 파일의 변경이력을 기록하여 관리를 용이하게 해준다.

-

### VCS의 이점

- 변경 내용 공유 가능
- 과거 상태로 복구 용이
- 여러 분기(*branch*)를 통해 병렬 관리 가능

-

### git을 쓰는 이유?

- 변경 내용을 추적하여 이전 상태로 복원이 가능하다.
- 빠른 속도
- 누구나 읽어갈 수 있도록 공개 가능
- 오프라인 상태에서도 작업 가능
- 복잡한 *Branch* 관리에 적합
- 병렬 개발 및 버전 관리가 용이해 생산성 증가
- *local repository*와 *remote repository*의 분리    
  -> 소스코드 분산관리   
  -> *local* 혹은 *remote repository* 중 하나가 심각한 피해를 입더라도 분산된 코드로 전체 코드 복원 가능
- 다양한 서비스 업체 및 보조 툴이 존재

-

### 주요 용어

- **Working Directory** : 실제 작업하고 있는 공간   
: *git*은 *working directory*의 변경사항을 지속적으로 체크하고 반영하는 동기화 작업을 한다.
- **Staging Area(Index)** – 변경이력의 임시 저장 공간   
: *working directory*에서 `add` 명령어을 통해 변경내용을 스테이지에 저장된다. 이 공간에 올려져 있는 파일만 *local repository*에 추가할 수 있다. 커밋 히스토리는 절대 수정이 되지 않기 때문에 다시 한 번 체크하기 위해 스테이지가 존재한다.
- **Local Repository** – 작업중인 컴퓨터의 변경이력 저장 공간   
: 오프라인 작업 시 이 공간까지 접근이 가능하며 매우 빠르다. *remote repository*에 손실이 발생하더라도 빠른 복구가 가능하다.
- **Remote Repository** – 외부 서버에 있는 저장 공간   
: 인터넷을 이용하여 접근 가능하다. 여러 사용자로부터 관리되는 각 *local repository*의 접점

-

### 주요 명령어

- `Add` – 작업 내용 중 일부 혹은 전체를 *stage area*로 저장하는 작업
- `commit` – *stage area*의 작업 내용을 *local repository*에 저장하는 작업   
: 각각의 `commit`은 의미 있는 변경 단위이고, 변경에 대한 설명을 *commit message*로 남긴다. 
- `push` – *local repository*의 내용을 *remote repository*로 보내는 작업 
- `pull` – *remote repository*로부터 *working directory*로 변경 내용을 가져오는 것
- `branch` – 새로운 `commit`을 쌓을 수 있는 가지를 만드는 것, 혹은 그 가지   
: *branch* 중 개발의 주축이 되는 *branch*를 ***master Branch***라 하며, 모든 *branch*는 ***master branch***에서 분기되어 최종적으로 다시 ***master branch***에 병합(*merge*)되어 개발이 진행된다.
- `merge` – 하나의 *branch*를 다른 *branch*와 합치는 과정   

- gitignore - `commit`에 포함하지 않을 파일 관리

-

### git flow

![git flow](https://camo.githubusercontent.com/6504c9df8922f9187037558bf1dad2dd57fd6c45/68747470733a2f2f696c6c75737472617465642d6769742e72656164746865646f63732e6f72672f656e2f6c61746573742f5f696d616765732f6769742d666c6f77732e737667)
> *workspace* 내용을 *stage*에 `add`하여 *local repository*에 `commit`하고, 이를 `push`해서 *remote repository*로 보낸다.   

<br>

![git flow2](https://www.cs.swarthmore.edu/~newhall/unixhelp/git.jpg)
> 공동작업자와 하나의 *remote repository*를 공유할 때 `add`와 `commit`은 각자의 *local* 영역에서 이루어지고 `push`와 `pull` 작업 시 공유하고 있는 *remote repository*에 접근하여 작업 내용을 동기화한다.  
> 다른 작업자가 `push`를 했을 경우(`pull`할 내용이 생겼을 경우) 먼저 `pull`을 해서 동기화를 한 다음에 `push` 할 수 있다.

-

### git vs. github

> *github*은 *git*이 아니다. *git*은 버전관리를 해주는 총괄적인 의미이고 *github*은 *git*에 사용될 *remote repository*를 제공해주는 *git*의 웹 기반 솔루션이다. 이 외에도 이슈 관리, 머지 요청 관리, 팀원 관리 등 전반적인 프로젝트 관리 기능도 함께 제공한다. 웹 기반으로 동작하기 때문에 인터넷 환경만 갖추어지면 접근이 가능하다. 기타 서비스로는 NAVER D2, GitLab, Bitbucket이 있다.
