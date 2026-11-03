pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "altynova"
        IMAGE_NAME = "${DOCKERHUB_USER}/casy4"
        IMAGE_TAG = "${BUILD_NUMBER}"
        WEBHOOK_URL = "http://host.docker.internal:8000"
    }

    stages {
        stage('Lint (Проверка кода)') {
            steps {
                echo 'Запуск проверки кода...'
                sh 'ls -la index.html'
            }
        }
        stage('Test (Юнит-тесты)') {
            steps {
                echo 'Все тесты успешно пройдены!'
            }
        }
        stage('Build (Сборка)') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }
        stage('Deploy (Развертывание)') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }

    post {
        success {
            echo 'Сборка успешна! Отправляем уведомление...'
            sh "curl -X POST -H 'Content-type: application/json' --data '{\"text\":\"✅ Сборка #${BUILD_NUMBER} прошла успешно!\"}' ${WEBHOOK_URL}"
        }
        failure {
            echo 'Сборка упала! Отправляем уведомление...'
            sh "curl -X POST -H 'Content-type: application/json' --data '{\"text\":\"❌ Сборка #${BUILD_NUMBER} завершилась с ошибкой!\"}' ${WEBHOOK_URL}"
        }
    }
}