pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/Morricano/iis-lpnu-rak.git'
        PROJECT_DIR = 'iis-lpnu-rak'
        SONARQUBE_ENV = 'Lab11-sonar-server-rak' 
    }

    stages {
        stage('Очистити робочу директорію') {
            agent { node { label 'vadym-aws-agent' } }
            steps {
                sh 'rm -rf $PROJECT_DIR || true'
            }
        }

        stage('Клонування репозиторію') {
            agent { node { label 'vadym-aws-agent' } }
            steps {
                sh "git clone $REPO_URL"
            }
        }

        stage('Оновлення системи та інсталяція NPM') {
            agent { node { label 'vadym-aws-agent' } }
            steps {
                sh '''
                    sudo apt-get update
                    sudo apt-get install -y npm
                '''
            }
        }

        stage('Встановлення залежностей') {
            agent { node { label 'vadym-aws-agent' } }
            steps {
                dir(PROJECT_DIR) {
                    sh 'npm install --legacy-peer-deps'
                }
            }
        }

        stage('Запуск збірки') {
            agent { node { label 'vadym-aws-agent' } }
            steps {
                dir(PROJECT_DIR) {
                    sh 'npm run build'
                }
            }
        }

        stage('Аналіз SonarQube') {
            agent { node { label 'vadym-aws-agent' } }
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    dir(PROJECT_DIR) {
                        sh 'sonar-scanner'
                    }
                }
            }
        }
    }
}
