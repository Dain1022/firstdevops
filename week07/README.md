 컨테이너 하나 = 역할 하나
• 앱과 DB를 나눠서 띄워야 함
▪ compose.yaml = 여러 컨테이너를 파일 하나(코드)로
▪ 서비스 이름이 곧 DNS 주소 (REDIS_HOST: "redis")
▪ 볼륨 = 컨테이너는 일회용, 데이터는 영구 / down -v는 데이터까지 삭제
▪ ports: 를 안 쓴 서비스는 외부에 노출되지 않음
▪ .env로 비밀번호 분리
▪ nginx로 요청 분배
▪ healthcheck로 '준비 완료' 확인

.env를 Git에 올리지 않는 이유
