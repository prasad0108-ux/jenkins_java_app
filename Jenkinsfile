pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "prasads01/jenkins-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/prasad0108-ux/jenkins_java_app.git',
                    credentialsId: 'github-pat'
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
                sh '''
                  docker rm -f jenkins-demo || true
                  docker run -d --name jenkins-demo -p 8080:8080 $DOCKER_IMAGE:$BUILD_NUMBER
                '''
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
