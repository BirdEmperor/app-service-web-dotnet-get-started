pipeline {
    agent any

    environment {
        // Путь, который мы создавали вчера
        DEPLOY_PATH = "/var/www/dotnet-app"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Команда сборки .NET
                sh 'dotnet publish -c Release -o ./publish'
            }
        }

        stage('Deploy') {
            steps {
                // Используем ключ ssh-local, который добавили в Шаге 2
                sshagent(['ssh-local']) {
                    // 1. Останавливаем сервис (игнорируем ошибку, если он не запущен)
                    sh "ssh -o StrictHostKeyChecking=no jenkins@localhost 'sudo systemctl stop webapp || true'"
                    
                    // 2. Чистим папку и копируем новые файлы
                    sh "ssh -o StrictHostKeyChecking=no jenkins@localhost 'rm -rf ${DEPLOY_PATH}/*'"
                    sh "scp -o StrictHostKeyChecking=no -r ./publish/* jenkins@localhost:${DEPLOY_PATH}"
                    
                    // 3. Запускаем сервис
                    sh "ssh -o StrictHostKeyChecking=no jenkins@localhost 'sudo systemctl start webapp'"
                }
            }
        }
    }
}
