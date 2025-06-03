pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/Morricano/iis-lpnu-rak.git'
        SONARQUBE_ENV = 'Lab11-sonar-server-rak'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: "${REPO_URL}", branch: 'main'
            }
        }

        stage('Installing NPM (if needed)') {
            steps {
                sh '''
                if ! command -v npm &> /dev/null
                then
                    sudo apt-get update
                    sudo apt-get install -y npm
                else
                    echo "npm already installed"
                fi
                '''
            }
        }

        stage('Installing dependencies') {
            steps {
                sh 'npm install --legacy-peer-deps'
            }
        }

        stage('Running dev server (test only)') {
            steps {
                sh '''
                nohup npm run dev > dev.log 2>&1 &
                DEV_PID=$!
                sleep 10
                kill $DEV_PID
                echo "Dev server started and stopped successfully."
                '''
            }
        }

        stage('SonarQube analysis') {
            steps {
                script {
                    def scannerHome = tool 'Lab11_scanner_rak'
                    withSonarQubeEnv("${SONARQUBE_ENV}") {
                        sh 'echo Поточні файли:' 
                        sh 'ls -la'
                        sh 'echo Вміст sonar-project.properties:' 
                        sh 'cat sonar-project.properties || echo "❌ Файл не знайдено!"'
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('Wait for SonarQube Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
