# Dockerfile (FastAPI)
FROM python:3.11-slim

# 시스템 의존성 설치
RUN apt-get update && apt-get upgrade -y && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# 작업 디렉토리 설정
WORKDIR /apps

ARG GIT_BRANCH_NAME
ARG APP_DIR_NAME
ARG GIT_URI

# Clone the Git repository. Here we dynamically specify the repository name using the variable defined earlier.
RUN git clone -b ${GIT_BRANCH_NAME} ${GIT_URI} ${APP_DIR_NAME}

# Changes the working directory to /app/${REPO_NAME}. This uses the variable to dynamically set the directory path.
WORKDIR /apps/${APP_DIR_NAME}/app

# RUN pip install --no-cache-dir -r ./requirements.txt
RUN pip install --no-cache-dir -r ./requirements.txt

# RUN rm -rf .git

# FastAPI 실행 (컨테이너 실행 시)
WORKDIR /apps/${APP_DIR_NAME}

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
