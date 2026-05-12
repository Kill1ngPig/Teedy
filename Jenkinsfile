pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'kill1ngpig/teedy'
        DOCKER_TAG = 'latest'
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
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "cd /mnt/c/Users/10623/.jenkins/workspace/teedy && DOCKER_BUILDKIT=0 /usr/bin/docker build -t kill1ngpig/teedy:%BUILD_NUMBER% ."'
            }
        }

        stage('Upload Image') {
            steps {
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker push kill1ngpig/teedy:%BUILD_NUMBER%"'
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker tag kill1ngpig/teedy:%BUILD_NUMBER% kill1ngpig/teedy:latest"'
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker push kill1ngpig/teedy:latest"'
            }
        }

        stage('Run containers') {
            steps {
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker rm -f teedy-container-8082 || true"'
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker rm -f teedy-container-8083 || true"'
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker rm -f teedy-container-8084 || true"'

                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker run -d -p 8082:8080 --name teedy-container-8082 kill1ngpig/teedy:%BUILD_NUMBER%"'
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker run -d -p 8083:8080 --name teedy-container-8083 kill1ngpig/teedy:%BUILD_NUMBER%"'
                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker run -d -p 8084:8080 --name teedy-container-8084 kill1ngpig/teedy:%BUILD_NUMBER%"'

                bat 'wsl -d Ubuntu-22.04 -u root bash -lc "/usr/bin/docker ps --filter name=teedy-container"'
            }
        }
    }
}