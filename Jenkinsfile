pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/Morricano/iis-lpnu-rak.git'
        PROJECT_DIR = 'iis-lpnu-rak'
         SONARQUBE_ENV = 'Lab11-sonar-server-rak' 
    }

    stages {
        stage('Очистити робочу директорію') {
            steps {
                sh 'rm -rf $PROJECT_DIR || true'
            }
        }

        stage('Клонування репозиторію') {
            steps {
                sh "git clone $REPO_URL"
            }
        }

        stage('Оновлення системи та інсталяція NPM') {
            steps {
                sh '''
                    sudo apt-get update
                    sudo apt-get install -y npm
                '''
            }
        }

        stage('Встановлення залежностей') {
            steps {
                dir(PROJECT_DIR) {
                    sh 'npm install --legacy-peer-deps'
                }
            }
        }

        stage('Запуск збірки') {
            steps {
                dir(PROJECT_DIR) {
                    sh 'npm run build'
                }
            }
        }

        stage('Аналіз SonarQube') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    dir(PROJECT_DIR) {
                        sh 'sonar-scanner'
                    }
                }
            }
        }

        stage('Інформація про IP') {
            steps {
                sh "echo IP: \$(hostname -I)"
            }
        }
    }
}
