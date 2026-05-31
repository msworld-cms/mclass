pipeline {
    agent any
    tools {
        maven 'maven 3.9.12'
    }
    environment {
        DOCKER_IMAGE = "demo-app"
        CONTAINER_NAME = "springboot-container"
        JAR_FILE_NAME = "app.jar"
        PORT = "8081"
        REMOTE_USER = "ec2-user"
        REMOTE_HOST = "15.165.119.6"
        REMOTE_DIR = "/home/ec2-user/deploy"
        SSH_CREDENTIALS_ID = "573bfa62-474d-412a-8edf-80d96f2dadbc"
    }

    stages {
        stage('Git Checkout') {
            steps { // step : stage 안에서 실행할 실제 명령어
                // Jenkins가 연결된 Git 저장소에서 최신 코드 체크아웃
                checkout scm
            }
        }
        stage('Maven Build') {
            steps {
                // 테스트는 건너뛰고 Maven 빌드
                sh 'mvn clean package -DskipTests'
                // sh 'echo Hello' : 리눅스 명령어 실행
            }
        }
        stage('Prepare Jar') {
            steps {
                // 빌드 결과물인 JAR 파일을 지정한 이름(app.jar)으로 복사
                sh 'cp target/demo-0.0.1-SNAPSHOT.jar ${JAR_FILE_NAME}'
            }
        }
        stage('Copy to Remote Server') {
            steps {
                // Jenkins가 원격 서버에 SSH 접속할 수 있도록 sshagent 사용
                sshagent (credentials: [env.SSH_CREDENTIALS_ID]) {
                    // 원격 서버에 배포 디렉토리 생성 (없으면 새로 만듦)
                    sh "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ${REMOTE_USER}@${REMOTE_HOST} \"mkdir -p ${REMOTE_DIR}\""
                    // JAR 파일과 Dockerfile을 원격 서버에 복사
                    sh "scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ${JAR_FILE_NAME} Dockerfile ${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_DIR}/"
                }
            }
        }
        stage('Remote Docker Build & Deploy') {
            steps {
                sshagent (credentials: [env.SSH_CREDENTIALS_ID]) {
                    sh """
ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ${REMOTE_USER}@${REMOTE_HOST} '
                    cd ${REMOTE_DIR}
                    docker rm -f ${CONTAINER_NAME} || true
                    docker build -t ${DOCKER_IMAGE} .
                    docker run -d --name ${CONTAINER_NAME} -p ${PORT}:${PORT} ${DOCKER_IMAGE}
'
"""
                }
            }
        }
        
    }
}