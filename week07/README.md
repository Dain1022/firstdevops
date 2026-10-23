# 7주차

---

## 오늘 배운 것

### 1. 도커 컴포즈 (Docker Compose)

#### 1) 컴포즈란?

- Compose의 원래 의미: 여러 요소를 조합하여 하나의 완성된 것을 만들다
- 독립된 컨테이너들이 **함께 동작하도록 구성 및 관리**하는 도구
- **컨테이너 하나 = 역할 하나** → 앱과 DB를 나눠서 띄워야 한다.
- `compose.yaml` = 여러 컨테이너를 **파일 하나(코드)** 로 정의

#### 2) 핵심 개념 정리

| 개념 | 설명 |
|------|------|
| **서비스 이름 = DNS 주소** | 같은 네트워크 안에서 서비스 이름으로 접속 가능 (예: `REDIS_HOST: "redis"`) |
| **볼륨** | 컨테이너는 일회용, 데이터는 영구. `down -v`는 데이터까지 삭제 |
| **ports 생략** | `ports:`를 안 쓴 서비스는 외부에 노출되지 않음 |
| **.env** | 비밀번호를 `compose.yaml`에서 분리 |
| **nginx** | 요청을 여러 컨테이너로 분배 |
| **healthcheck** | '준비 완료' 상태인지 확인 |

#### 3) Redis

- 메모리 기반의 빠른 **키-값 저장소**
- 디스크에도 기록 가능

#### 4) compose.yaml 추천 작성 순서

1. `image`, `build`
2. `command`
3. `ports`, `expose`
4. `environment`
5. `volumes`
6. `depends_on`
7. `healthcheck`
8. `restart`
9. `develop`

#### 5) 설정 우선순위

```text
코드 기본값  <  Docker ENV  <  compose environment
```

> 뒤로 갈수록 우선순위가 높다.

---

### 2. 실습

#### 1) compose.yaml

#### 2) YAML 주의 사항

- 같은 단계의 키(key)는 **같은 칸(레벨)** 에서 시작
- 숫자처럼 보이는 값은 **따옴표**로 감싸면 안전 (예: `"8080:5000"`)
- 들여쓰기는 **스페이스만** 사용 (탭 사용 금지)

#### 3) 유효성 검사

```bash
docker compose config -q
```

- 문법에 문제가 없으면 아무 출력 없이 끝난다.

#### 4) 자주 쓰는 명령어

```bash
docker compose up -d        # 백그라운드로 전체 실행
docker compose ps           # 서비스 상태 확인
docker compose logs         # 로그 확인
docker compose down         # 컨테이너, 네트워크 삭제 (볼륨 유지)
docker compose down -v      # 컨테이너, 네트워크, 볼륨까지 삭제
```

---

### 3. 볼륨 분리

- 컨테이너를 통째로 지웠는데도 **데이터가 남아 있었다.**
- 이유: 컨테이너를 삭제해도 별도로 생성한 **볼륨(Volume)은 기본적으로 삭제하지 않기** 때문
- 컨테이너를 삭제해도 별도로 생성한 볼륨은 유지된다.

---

### 4. .env로 비밀번호 분리

- 문제: 비밀번호가 `compose.yaml`에 그대로 적혀 있었다.
<img width="386" height="116" alt="스크린샷 2026-10-23 164031" src="https://github.com/user-attachments/assets/fbd829cb-b78b-475c-8c5c-ff594808c9b5" />


```bash
echo "REDIS_PASSWORD=비밀번호" > .env
cp .env .env.example   # 이후 .env.example에서는 실제 값을 지운다
```

| 파일 / 문법 | 하는 일 |
|-------------|---------|
| `.env` | `이름=값`을 한 줄씩 작성. `compose.yaml`과 같은 폴더에 있으면 자동으로 반영 |
| `${REDIS_PASSWORD}` | 실행할 때 `.env`의 값으로 바뀌어 들어가는 **빈칸** |
| `.env.example` | 값은 제거하고 **어떤 항목이 필요한지** 알려 주는 파일. 이를 참고하여 본인의 `.env` 작성 (옵션) |
| `.gitignore`의 `.env` | Git이 `.env`를 모른 척하여 **실수로 커밋하는 것을 방지** |
| `.dockerignore`의 `.env` | `COPY . .`을 해도 **이미지 안에 복사되지 않음** |

---

### 5. healthcheck / scale

- **healthcheck**: 컨테이너가 떠 있는 것과 '준비 완료'인 것은 다르다. 서비스가 실제로 응답하는지 확인한다.
- **scale**: 같은 서비스를 여러 개 띄워 부하를 나눈다. (nginx가 요청을 분배)

```bash
docker compose up -d --scale app=3
```

---

### 6. 요즘 도커

- Docker Desktop에 **AI 기능**이 포함되어 있다.

---


### 1. `down` 후 `up` vs `down -v` 후 `up` 차이 비교

| 구분 | `down` 후 `up` | `down -v` 후 `up` |
|------|----------------|-------------------|
| 컨테이너 | 삭제 후 새로 생성 | 삭제 후 새로 생성 |
| 네트워크 | 삭제 후 새로 생성 | 삭제 후 새로 생성 |
| **볼륨** | **유지됨** | **삭제됨** |
| **데이터** | **그대로 남아 있음** | **모두 사라지고 빈 상태로 시작** |

```bash
# 1) 데이터를 남긴 뒤 down → up
docker compose down
docker compose up -d
# 결과: 이전에 저장한 데이터가 그대로 보인다.

# 2) down -v → up
docker compose down -v
docker compose up -d
# 결과: 데이터가 모두 사라지고 처음 상태로 시작한다.
```

> 한 줄 요약: **컨테이너는 일회용이라 지워도 되지만, 데이터는 볼륨에 있어서 `down`으로는 남고 `down -v`로 지우면 사라진다.**

> `down -v`는 데이터를 되돌릴 수 없으니, 중요한 데이터가 있을 때는 주의해야 한다.

### 2. `.env`를 Git에 올리지 않는 이유

- **비밀번호, API 키 같은 민감 정보가 들어 있기 때문**이다.
- Git에 한 번 올라가면 **커밋 기록에 계속 남는다.** 나중에 파일을 지워도 과거 기록에서 복구할 수 있다.
- 저장소가 공개(public)라면 **누구나 비밀번호를 볼 수 있어** 계정 탈취나 데이터 유출로 이어질 수 있다.
- 비밀번호는 사람마다, 환경마다(개발/운영) 다르므로 **코드와 분리해서 관리**하는 것이 맞다.
- 그래서 `.gitignore`에 `.env`를 등록하고, 값을 뺀 `.env.example`만 올려 **어떤 항목이 필요한지만 공유**한다.


```gitignore
# .gitignore
.env
```
<img width="851" height="45" alt="image" src="https://github.com/user-attachments/assets/85ed0304-772b-4335-99a0-dc5284c40631" />
