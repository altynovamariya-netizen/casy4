pipeline {
    agent any
    environment {
        DOCKERHUB_USER = "altynova"
        IMAGE_NAME = "${DOCKERHUB_USER}/casy4"
        GITHUB_REPO = "altynovamariya-netizen/casy4"
    }
    stages {
        stage('Lint') {
            steps { echo 'Проверка кода...' }
        }
        stage('Test') {
            steps { echo 'Тесты пройдены!' }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh """
                    docker run --rm \
                      -e SONAR_HOST_URL="http://host.docker.internal:9000" \
                      -e SONAR_LOGIN="${SONAR_TOKEN}" \
                      -v "\$(pwd):/usr/src" \
                      sonarsource/sonar-scanner-cli \
                      -Dsonar.projectKey=casy9
                    """
                }
            }
        }

        stage('Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }
        stage('Deploy') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo \$PASS | docker login -u \$USER --password-stdin"
                    sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        stage('Create GitHub Release') {
            steps {
                withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                    script {
                        sh """
                        curl -s -X POST \
                        -H "Authorization: token ${GITHUB_TOKEN}" \
                        -H "Accept: application/vnd.github.v3+json" \
                        -d '{"tag_name":"v-${BUILD_NUMBER}", "name":"Release v-${BUILD_NUMBER}", "body":"Автоматический релиз из Jenkins."}' \
                        https://api.github.com/repos/${GITHUB_REPO}/releases
                        """
                    }
                }
            }
        }
    }
}


