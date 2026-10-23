# 6주차
---

## 오늘 배운 것

### 1. Dockerfile

#### 1) Dockerfile이란?

- 이미지를 만드는 **레시피**
- 실행 환경을 **코드로 정의**한 파일
- **코드로서의 인프라 (Infrastructure as Code)** 사고방식과 연결됨
- DevOps의 핵심 사상
- 명령을 **위에서부터 순서대로** 처리하며 이미지를 생성

#### 2) 주요 명령어

| 명령어 | 설명 |
|--------|------|
| `FROM` | 기반이 될 베이스 이미지 지정 |
| `WORKDIR` | 작업 디렉터리 지정 |
| `COPY` | 호스트의 파일을 이미지 안으로 복사 |
| `RUN` | **이미지를 만들 때** 한 번 실행 (예: 패키지 설치) |
| `EXPOSE` | 컨테이너가 사용하는 포트 표시 |
| `CMD` | **컨테이너를 실행할 때마다** 실행 |
| `ENV` | 환경변수 설정 |

>  `RUN` = 빌드할 때 한 번, `CMD` = 컨테이너 시작할 때마다

#### 3) Dockerfile 예시

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
ENV APP_TITLE="My Guestbook"
EXPOSE 5000
CMD ["python", "app.py"]
```

#### 4) 빌드 캐시

- 도커는 빌드 단계(레이어)마다 캐시를 판단한다.
- 이전 빌드와 같은 단계는 다시 실행하지 않고 캐시를 재사용한다.

> **기억해야 할 것!!!**
> 한 레이어가 바뀌면 그 아래 레이어는 전부 다시 빌드*
> → 자주 바뀌는 파일(소스 코드)은 Dockerfile 아래쪽에, 잘 안 바뀌는 것(패키지 설치)은 위쪽에 두는 것이 효율적.

#### 5) 이미지 정리 (rmi)

```bash
docker rmi guestbook:bad    # 이미지 삭제
docker images               # 삭제됐는지 확인
```

#### 6) 실습: 4곳 커스텀해보기



---

### 2. 실습: 친구와 이미지 공유하기

#### 1) 실습 순서

```bash
# 내 컨테이너 정리
docker rm -f gb

# 친구 이미지로 컨테이너 실행
docker run -d -p 9090:5000 --name friend ghcr.io/친구아이디/guestbook:v1

# (참고) docker pull로 먼저 받고 run 해도 됨
docker pull ghcr.io/친구아이디/guestbook:v1
```

#### 2) 사용한 옵션

| 옵션 | 설명 |
|------|------|
| `-d` | 백그라운드 실행 |
| `-p 9090:5000` | 내 PC의 9090 포트 → 컨테이너의 5000 포트 연결 |
| `--name friend` | 컨테이너 이름 지정 |
| `rm -f` | 실행 중인 컨테이너도 강제 삭제 |

#### 3) 확인한 것

- 브라우저에서 `http://localhost:9090` 으로 접속해 친구의 앱이 실행되는 것을 확인했다.

> 이미지는 프로그램을 공유하는 것이지, 데이터까지 공유하는 것이 아니다.
> 그래서 내가 남긴 방명록은 친구 이미지로 띄운 앱에서 보이지 않는다.
> (데이터는 컨테이너 안에 저장되어 있고 컨테이너는 이미지와 별개이기 때문)

#### 4) 정리하기

```bash
docker rm -f friend                          # 친구 컨테이너 삭제
docker rmi ghcr.io/친구아이디/guestbook:v1    # 친구 이미지 삭제
```

---


### 1. Dockerfile의 각 줄이 하는 일

| 줄 | 하는 일 |
|----|---------|
| `FROM python:3.12-slim` | 파이썬이 설치된 가벼운 이미지를 출발점으로 삼는다. |
| `WORKDIR /app` | 컨테이너 안에서 작업할 폴더를 `/app`으로 정한다. 이후 명령은 이 폴더 기준으로 실행된다. |
| `COPY requirements.txt .` | 패키지 목록 파일만 먼저 이미지 안으로 복사한다. |
| `RUN pip install -r requirements.txt` | 필요한 라이브러리를 설치한다. 이미지를 만들 때 한 번만 실행된다. |
| `COPY . .` | 나머지 소스 코드를 이미지 안으로 복사한다. |
| `ENV APP_TITLE="My Guestbook"` | 환경변수의 기본값을 설정한다. |
| `EXPOSE 5000` | 이 앱이 5000번 포트를 사용한다고 알려 준다. (문서 역할) |
| `CMD ["python", "app.py"]` | 컨테이너가 시작될 때마다 앱을 실행한다. |

### 2. 빌드 캐시가 동작한 로그 (CACHED)

같은 Dockerfile을 두 번째로 빌드했을 때, 바뀌지 않은 단계는 `CACHED`로 표시되었다.

```text
=> CACHED [2/5] WORKDIR /app
=> CACHED [3/5] COPY requirements.txt .
=> CACHED [4/5] RUN pip install -r requirements.txt
=> [5/5] COPY . .
```

### 3. 설정을 넣는 세 가지 방법 비교

| 방법 | 어디에 | 바꾸려면 | 특징 |
|------|-------------|----------|------|
| **코드** | 소스 코드에 직접 작성 | 코드 수정 후 **다시 빌드** | 이미지에 고정됨 |
| **ENV** | Dockerfile에 작성 | Dockerfile 수정 후 **다시 빌드** | 이미지의 **기본값**. 실행할 때 덮어쓸 수 있음 |
| **`-e`** | `docker run` 할 때 입력 | **다시 빌드 없이** 실행 때마다 변경 | 컨테이너마다 다르게 설정 가능. 우선순위가 가장 높음 |

```bash
# 예시: ENV로 정해 둔 기본값을 -e로 덮어쓰기
docker run -d -p 8080:5000 -e APP_TITLE="우리 반 방명록" --name gb guestbook:v1
```

> 한 줄 요약: **코드**는 이미지에 박힌 값, **ENV**는 이미지의 기본값, **`-e`**는 실행할 때 바꾸는 값이다.

ghcr.io/dain1022/guestbook:v2 : 내 이미지 주소
4곳 이상 커스텀해보기
<img width="623" height="595" alt="스크린샷 2026-10-23 160426" src="https://github.com/user-attachments/assets/f3a9a63d-1631-4d4a-b305-dfdb3748e72b" />
