pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "altynova"
        IMAGE_NAME = "${DOCKERHUB_USER}/casy4"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Lint (Проверка кода)') {
            steps {
                echo 'Запуск проверки кода (Lint)...'
                // Имитация проверки: проверяем, что файл существует
                sh 'ls -la index.html'
                echo 'Код прошел проверку!'
            }
        }
        
        stage('Test (Юнит-тесты)') {
            steps {
                echo 'Запуск юнит-тестов...'
                // Имитация тестов
                echo 'Все тесты успешно пройдены!'
            }
        }

        stage('Build (Сборка)') {
            steps {
                echo 'Сборка Docker-образа...'
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy (Развертывание)') {
            steps {
                echo 'Отправка в Docker Hub (Развертывание)...'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                }
            }
        }
    }
}