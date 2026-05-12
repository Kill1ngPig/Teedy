pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'kill1ngpig/teedy'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
        WSL_WORKSPACE = "/mnt/c/Users/10623/.jenkins/workspace/teedy"
    }

    stages {
        stage('Build') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/master']],
                    extensions: [],
                    userRemoteConfigs: [[url: 'https://github.com/Kill1ngPig/Teedy.git']]
                )
                bat 'mvn -B -DskipTests clean package'
            }
        }

        stage('Building image') {
            steps {
                bat 'wsl -u root bash -lc "cd ${WSL_WORKSPACE} && docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."'
            }
        }

        stage('Upload Image') {
            steps {
                bat 'wsl -u root bash -lc "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"'
                bat 'wsl -u root bash -lc "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"'
                bat 'wsl -u root bash -lc "docker push ${DOCKER_IMAGE}:latest"'
            }
        }

        stage('Run containers') {
            steps {
                bat 'wsl -u root bash -lc "docker rm -f teedy-container-8082 || true"'
                bat 'wsl -u root bash -lc "docker rm -f teedy-container-8083 || true"'
                bat 'wsl -u root bash -lc "docker rm -f teedy-container-8084 || true"'

                bat 'wsl -u root bash -lc "docker run -d -p 8082:8080 --name teedy-container-8082 ${DOCKER_IMAGE}:${DOCKER_TAG}"'
                bat 'wsl -u root bash -lc "docker run -d -p 8083:8080 --name teedy-container-8083 ${DOCKER_IMAGE}:${DOCKER_TAG}"'
                bat 'wsl -u root bash -lc "docker run -d -p 8084:8080 --name teedy-container-8084 ${DOCKER_IMAGE}:${DOCKER_TAG}"'

                bat 'wsl -u root bash -lc "docker ps --filter name=teedy-container"'
            }
        }
    }
}