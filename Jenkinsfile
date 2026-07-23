pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
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
                withSonarQubeEnv('SonarQubeServer') {
                    bat 'sonar-scanner'
                }
            }
        }

        

        stage('Docker Build') {
            steps {
                bat "docker build -t your-dockerhub-username/ci-cs-demo:%BUILD_NUMBER% ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
    sh 'echo $PASS | docker login -u $USER --password-stdin'
    sh 'docker push ilangoip3008/ci-cd-demo:${BUILD_NUMBER}'
}

                }
            }
        }

        stage('Deploy') {
            steps {
                bat "docker run -d -p 8080:8080 your-dockerhub-username/ci-cs-demo:%BUILD_NUMBER%"
            }
        }
    }
}
