pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "html-poc-image"
        SONARQUBE = "sonarqube"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/your-username/html-poc.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                    sonar-scanner \
                    -Dsonar.projectKey=html-poc
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker rm -f html-poc-container || true
                docker run -d \
                --name html-poc-container \
                -p 80:80 \
                $DOCKER_IMAGE
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Pipeline Failed!"
        }
    }
}
