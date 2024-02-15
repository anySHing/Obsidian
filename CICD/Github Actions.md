기존에는 Jenkins, Buildkite, circleci와 같은 별도의 툴들을 이용했으나 현재 Github Actions에서 자동화 프로세스를 지원

Github Actions는 특정한 이벤트가 발생했을 때 내가 원하는 일을 자동으로 수행할 수 있도록 만들어주는 툴

> 핵심 기능

1. Event: github에서 발생할 수 있는 대부분의 이벤트들을 지정할 수 있음
	- main 브랜치로 머지 / 커밋을 푸쉬 / 이슈를 누군가 열면
2. Workflow:
	- 특정한 이벤트가 발생했을 경우 어떤 일을 수행할 지 지정
3. Job:
	- Workflow에서 수행할 일들
	- 기본적으로는 병렬적으로 수행됨. 순차적으로 진행되도록도 가능함
	- Shell Script를 사용해서 작성
4. Action:
	- 재사용할 수 있는 공개적으로 오픈된 작업들을 제공함.(github에서)
	- 프리셋같은거
5. Runner:
	- Job을 실행하는것
	- VM이라고 볼 수도, Container라고 볼 수도 있음

> 데모

1. 일단 프로젝트 루트 경로에 `.github/workflows/` 폴더 내에 `*.yaml` 파일을 만들어서 작성함.
```yaml
name: laern-github-actions
on: [push] # push 이벤트가 발생할 경우
jobs: # 어떤 job인지
  check-bats-version: # job의 이름
    runs-on: ubuntu-latest # runner의 종류
    steps: # 순서
      - uses: actions/checkout@v3 # checkout@v3 라는 action 사용
      - uses: actions/setup-node@v3 # setup-node@v3 라는 action 사용
        with: # node의 버전 명시
          node-version: '14'
	  - run: npm install -g bats
	  - run: bats -v
```