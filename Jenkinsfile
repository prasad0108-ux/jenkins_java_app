pipeline {
    agent any
    
    environment {
        // DockerHub credentials stored in Jenkins
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub-creds')
        DOCKER_IMAGE = "prasads01/jenkins-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from GitHub using PAT credentials
                git branch: 'main',
                    url: 'https://github.com/prasad0108-ux/jenkins_java_app.git',
                    credentialsId: 'github-pat'   // <-- Add your GitHub PAT credentials ID
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/']) {
                    sh 'docker push $DOCKER_IMAGE:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy (Run Container)') {
            steps {
                sh 'docker run -d -p 8080:8080 $DOCKER_IMAGE:$BUILD_NUMBER'
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs."
        }
    }
}
