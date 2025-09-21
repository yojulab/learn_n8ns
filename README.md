# learn_n8ns

## 프로젝트 개요

`learn_n8ns`는 FastAPI 기반의 백엔드와 n8n 워크플로우 자동화, MongoDB 데이터베이스를 연동하여 다양한 데이터 처리 및 자동화 기능을 실습할 수 있는 예제 프로젝트입니다.

## 주요 구성 요소

- **FastAPI**: RESTful API 서버 구현. CORS 설정 및 기본 라우트 제공.
- **MongoDB**: 데이터 저장 및 관리. 샘플 코드(`app/samples/sample_mongodb_connection.py`)로 연동 예시 제공.
- **n8n**: 오픈소스 워크플로우 자동화 툴. 도커 환경에서 sqlite DB로 실행.
- **Docker**: 모든 서비스(FastAPI, MongoDB, n8n)를 컨테이너로 실행. `docker-compose.yml` 및 `Dockerfile` 제공.

## 디렉토리 구조

```
learn_n8ns/
├── LICENSE
├── README.md
├── app/
│   ├── main.py                # FastAPI 서버 진입점
│   ├── requirements.txt       # Python 의존성 목록
│   ├── resources/
│   │   └── images/            # 정적 이미지 리소스
│   └── samples/
│       └── sample_mongodb_connection.py # MongoDB 연동 샘플
├── dockers/
│   ├── docker-compose.yml     # 전체 서비스 오케스트레이션
│   └── Dockerfile             # FastAPI 앱용 Dockerfile
```

## FastAPI 서버

- `app/main.py`에서 FastAPI 인스턴스 생성 및 CORS 설정.
- 기본 라우트(`/`, `/items/{item_id}`) 제공.
- (예정) `/images` 경로로 정적 이미지 서비스 가능.

## MongoDB 연동 샘플

- `app/samples/sample_mongodb_connection.py`에서 pymongo를 사용한 DB 연결, 데이터 입력 예시 제공.
- 로컬/원격 MongoDB 모두 연결 가능.

## n8n 워크플로우 자동화

- `dockers/docker-compose.yml`에서 n8n 서비스 정의.
- 기본 인증(admin/admin), sqlite DB, 사용자 데이터 폴더 지정.
- FastAPI, MongoDB와 연동 가능.

## Docker 환경 실행 방법

1. 환경 변수(GIT_BRANCH_NAME, APP_DIR_NAME, GIT_URI) 설정
2. `dockers` 디렉토리에서 다음 명령 실행:
	 ```bash
	 docker-compose up --build
	 ```
3. FastAPI: http://localhost:8000
4. n8n: http://localhost:5678 (기본 계정: admin/admin)
5. MongoDB: localhost:27017

## 의존성

- Python 패키지: `app/requirements.txt` 참고
	- fastapi, pymongo, uvicorn, jinja2 등
- Docker 이미지: python:3.11-slim, mongo:8, n8nio/n8n:latest

## 라이선스

Apache License 2.0