# 5주차

## 학습 목표 체크리스트

- [x] 하이퍼바이저(VM)와 컨테이너의 차이를 그림으로 설명할 수 있다.
- [x] 이미지와 컨테이너 관계를 설명할 수 있다.
- [x] `docker run`, `ps`, `logs`, `exec`, `stop`, `rm`, `images`와 주요 옵션을 사용할 수 있다.
- [x] 컨테이너를 활용하여 서로 다른 실행 환경을 만들고 확인할 수 있다.
- [x] Docker 실습을 GitHub Issue로 정의하고, Issue → Branch → Pull Request → Merge 과정을 통해 학습 결과를 기록할 수 있다.

---

## 오늘 배운 것

### 1. Docker

#### 1) 도커란?

- 애플리케이션을 **개발(Develop), 배포(Ship), 실행(Run)** 하기 위한 오픈 플랫폼
- 애플리케이션과 실행 환경을 **컨테이너**로 묶어서, 서로 다른 환경에서도 비교적 동일하게 실행 가능
- VM보다 상대적으로 가벼워 하나의 서버에서 여러 컨테이너를 효율적으로 운영 가능

> **개발 → 테스트 → 배포** 환경을 일관되게 유지할 수 있다.

#### 2) 구성

**Dockerfile로 직접 만드는 경우**

````text
Dockerfile
    ↓ build
  Image
    ↓ run
Container
````

**Docker Hub에서 가져오는 경우**

````text
Docker Hub
    ↓ pull
  Image
    ↓ run
Container
````

---

### 2. 이미지와 컨테이너

| 구분 | 설명 |
|------|------|
| **이미지** | 프로그램과 실행 환경을 함께 압축한 파일 |
| **컨테이너** | 이미지를 실행했을 때 만들어지는 격리된 환경 (그 안에서 프로그램이 프로세스로 실행됨) |
| **레지스트리** | 이미지 창고 (예: Docker Hub) |

> 컨테이너를 삭제하면 컨테이너에 저장된 변경사항도 **함께 삭제**된다.

---

### 3. 도커 명령어

#### 기본 명령어

````bash
docker ps                  # 실행 중인 컨테이너 목록
docker ps -a               # 모든 컨테이너 목록 (중지된 것 포함)
docker images              # 로컬에 있는 이미지 목록
docker stop <컨테이너>      # 컨테이너 중지
docker rm <컨테이너>        # 컨테이너 삭제
docker logs <컨테이너>      # 컨테이너 로그 확인
docker exec -it web bash   # 실행 중인 컨테이너 안에서 셸 실행
````

#### 주요 옵션

| 옵션 | 설명 |
|------|------|
| `-e` | 환경변수 주입 |
| `-it` | 대화형 실행 |
| `--rm` | 실행 종료 후 컨테이너 자동 삭제 |
| `-v` | 디렉터리 연결 (볼륨 마운트) |
| `-w` | 작업 디렉터리 지정 |

#### 옵션 사용 예시

````bash
# 환경변수를 주입하여 실행
docker run -e MY_ENV=hello ubuntu env

# 대화형으로 실행하고, 종료 시 컨테이너 삭제
docker run -it --rm ubuntu bash

# 현재 디렉터리를 컨테이너에 연결하고, 작업 디렉터리 지정
docker run -it --rm -v $(pwd):/app -w /app ubuntu bash
````

---

### 4. GitHub Issue 기반 작업 흐름

| 단계 | 역할 |
|------|------|
| **Issue** | 무엇을, 왜 할 것인가 |
| **Branch** | 해당 작업을 안전하게 진행하는 공간 |
| **Pull Request** | 변경 내용을 검토하고 합쳐 달라는 요청 |
| **Merge** | 변경 내용 반영 |

#### 진행 상황 체크

- [x] Issue 작성 (실습 내용 정의)
- [x] Branch 생성
- [x] 작업 및 Commit
- [x] Pull Request 생성
- [x] Merge

### 5주차 실습

- 1. 컨테이너3개를각각다른포트(8091, 8092, 8093)로, 이름은'nginx1', 'nginx2', 
'nginx3'으로 실행하기
- 2. 각컨테이너에'exec'로들어가서, 'index.html'을수정하여구분되게만들기
예) 'Welcome to nginx1' 등
- 3. 각페이지를브라우저에서확인후캡처, 캡처파일을week05/images/에저장
- 4. 'curl'로 세 페이지를확인한결과를'week05/README.md'에추가
- 5. 'docker ps' 결과 확인 후 캡처, 캡처파일을week05/images/에저장
- 6. 실습이끝난뒤, nginx1, nginx2, nginx3, web 등 컨테이너정리
<img width="973" height="77" alt="스크린샷 2026-10-23 152246" src="https://github.com/user-attachments/assets/07bbf518-4c66-403d-bca0-307d922100eb" />

<img width="446" height="122" alt="스크린샷 2026-10-23 152149" src="https://github.com/user-attachments/assets/ba4767a4-63ca-42c6-9599-7904fefd37f3" />


