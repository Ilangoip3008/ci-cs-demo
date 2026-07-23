pipeline {
    agent any

    environment {
        DOCKER_USER = 'ilangoip3008'
        IMAGE_NAME = 'ci-cs-demo'
        SONARQUBE = 'SonarQubeServer'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Ilangoip3008/ci-cs-demo.git',
                    credentialsId: 'ci-cs-repo1'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    bat 'sonar-scanner'
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat "docker build -t %DOCKER_USER%/%IMAGE_NAME%:%BUILD_NUMBER% ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'PASS')]) {
                    bat "docker login -u %DOCKER_USER% -p %PASS%"
                    bat "docker push %DOCKER_USER%/%IMAGE_NAME%:%BUILD_NUMBER%"
                }
            }
        }

        stage('Deploy') {
            steps {
                bat "docker rm -f ci-cs-demo || echo No old container"
                bat "docker run -d --name ci-cs-demo -p 8081:8080 %DOCKER_USER%/%IMAGE_NAME%:%BUILD_NUMBER%"
            }
        }
    }
}
