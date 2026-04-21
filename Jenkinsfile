pipeline {
    agent any
    
    environment {
        SCANNER_HOME    = tool 'sonar-scanner'
        SONAR_SERVER    = 'SonarQube'
        DOCKER_HUB_USER = 'bhanutejaravutla'
        IMAGE_NAME      = 'my-app'
        IMAGE_TAG       = 'latest'
    }

    stages {

        stage('Fetch Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/tejaravutla287/POC8.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONAR_SERVER}") {
                    sh "${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=my-poc"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-creds',
                        usernameVariable: 'bhanutejaravutla',
                        passwordVariable: 'Bhanu@145'
                    )
                ]) {
                    sh """
                        docker login -u $USER --password-stdin <<< "$PASS"
                        docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage('Deploy to Client (Local Run)') {
            steps {
                sh "docker rm -f my-app-container || true"
                sh "docker run -d -p 80:80 --name my-app-container ${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }
}
