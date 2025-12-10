# Cloudflare Tunnel 설정 가이드 (핵심 요약)

## 1. Cloudflared 설치

### PowerShell 관리자 권한 실행 후:

```powershell
# Chocolatey 설치
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# 버전 확인
choco --version

# Cloudflared 설치
choco install cloudflared -y

# 설치 확인
cloudflared --version
```

---

## 2. Cloudflare Tunnel 생성

```powershell
# 사용자 디렉토리로 이동
cd C:\Users\PC

# Cloudflare 로그인 (브라우저 열림)
cloudflared login
# 결과: C:\Users\PC\.cloudflared\cert.pem

# 터널 생성
cloudflared tunnel create test-docker-tunnel
# 결과: C:\Users\PC\.cloudflared\TUNNEL_ID.json (TUNNEL_ID 메모!)

# DNS 라우팅
cloudflared tunnel route dns test-docker-tunnel n8n.cocolabhub.store
cloudflared tunnel route dns test-docker-tunnel api.cocolabhub.store
```

---

## 3. Docker Compose 수정

**docker-compose.yml** - n8n 환경변수만 수정:

```yaml
  app_n8n:
    image: n8nio/n8n:latest
    container_name: n8n_app
    ports:
      - "5678:5678"
    environment:
      - GENERIC_TIMEZONE=Asia/Seoul
      - DB_TYPE=sqlite
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=admin
      
      # [변경] 도메인 설정
      - N8N_HOST=0.0.0.0
      - WEBHOOK_URL=https://n8n.cocolabhub.store/
      - N8N_EDITOR_BASE_URL=https://n8n.cocolabhub.store/
    volumes:
      - n8n_data:/home/node/.n8n
    restart: unless-stopped
```

---

## 4. Tunnel 설정 파일 생성

**C:\Users\PC\\.cloudflared\config.yml** 파일 생성:

```yaml
# YOUR_TUNNEL_ID를 실제 ID로 교체
tunnel: YOUR_TUNNEL_ID
credentials-file: C:\Users\PC\.cloudflared\YOUR_TUNNEL_ID.json

ingress:
  - hostname: n8n.cocolabhub.store
    service: http://localhost:5678
  
  - hostname: api.cocolabhub.store
    service: http://localhost:8000
  
  - service: http_status:404
```

---

## 5. 서비스 실행

```powershell
# Docker 컨테이너 시작
cd C:\your\project\directory
docker-compose up -d

# Cloudflare Tunnel 실행 (새 PowerShell 창)
cloudflared tunnel run test-docker-tunnel
```

### 백그라운드 실행 (선택사항):

```powershell
# Windows 서비스로 등록
cloudflared service install
cloudflared service start
```

---

## 6. 접속 확인

- **n8n**: https://n8n.cocolabhub.store
- **FastAPI**: https://api.cocolabhub.store/docs

---

## 주요 명령어

```powershell
# 터널 목록
cloudflared tunnel list

# 터널 정보
cloudflared tunnel info test-docker-tunnel

# 서비스 상태
sc query cloudflared

# Docker 로그
docker-compose logs -f app_n8n

# 컨테이너 재시작
docker-compose restart app_n8n
```

---

## 문제 해결

### 502 오류
```powershell
docker-compose restart
docker ps  # 컨테이너 실행 확인
```

### Tunnel 연결 안됨
- `config.yml`에서 `TUNNEL_ID` 확인
- `credentials-file` 경로가 실제 파일과 일치하는지 확인

### Webhook 작동 안함
```powershell
# docker-compose.yml 확인 후
docker-compose restart app_n8n
```