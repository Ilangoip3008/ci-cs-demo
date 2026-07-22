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
                    credentialsId: 'github-creds1'
            }
        }

        stage('Verify Python') {
            steps {
                bat '"C:\\Users\\acer\\AppData\\Local\\Programs\\Python\\Launcher\\py.exe" --version'
            }
        }

        stage('Build & Test') {
            steps {
                bat '"C:\\Users\\acer\\AppData\\Local\\Programs\\Python\\Launcher\\py.exe" -3 -m unittest discover'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    bat '"C:\\Users\\acer\\AppData\\Local\\Programs\\Python\\Launcher\\py.exe" -3 -m pip install sonar-scanner'
                    bat 'sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
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
                    bat "echo %PASS% | docker login -u %USER% --password-stdin"
                    bat "docker push your-dockerhub-username/ci-cs-demo:%BUILD_NUMBER%"
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
