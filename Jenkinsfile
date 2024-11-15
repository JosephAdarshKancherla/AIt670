pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                // Remove existing .war files if any
                sh 'rm -rf *.war'
                // Ensure target directory exists
                sh 'mkdir -p target'
                // Create the .war file
                sh 'jar -cvf target/static-website.war -C my-static-website/src/main/webapp .'
                // Build the Docker image
                sh 'docker build -t josephadarsh/ait670-app:latest .'
            }
        }
        stage('Login') {
            steps {
                // Login to Docker Hub
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Push image to Docker Hub') {
            steps {
                // Push the Docker image to Docker Hub
                sh 'docker push josephadarsh/ait670-app:latest'
            }
        }
        stage('Deploying on K8s') {
            steps {
                // Deploy the app on Kubernetes
                sh 'kubectl set image deployment/ait670assignment-deployment container-0=josephadarsh/ait670-app:latest -n default'
                sh 'kubectl rollout restart deploy ait670assignment-deployment -n default'
            }
        }
    }
    post {
        always {
            // Logout from Docker after the pipeline completes
            sh 'docker logout'
        }
    }
}
