pipeline {
    agent any
    
    stages {
        stage("Clone code") {
            steps {
                echo "Cloning the code from GitHub"
                git url: "https://github.com/12bhupendra/django-notes-app.git", branch: "main"
            }
        }
        stage("Build Docker Image") {
            steps {
                echo "Building Docker image"
                sh "docker build -t my-notes-app ."
            }
        }
        stage("Push to Docker Hub") {
            steps {
                echo "Pushing image to Docker Hub"
                withCredentials([usernamePassword(credentialsId: "dockerhub", passwordVariable: "dockerhubpass", usernameVariable: "dockerhubuser")]) {
                    sh "docker tag my-notes-app ${dockerhubuser}/my-notes-app:v1"
                    sh "docker login -u ${dockerhubuser} -p ${dockerhubpass}"
                    sh "docker push ${dockerhubuser}/my-notes-app:v1"
                }
            }
        }
        stage("Deploy") {
            steps {
                echo "Deploying the container"
                withCredentials([usernamePassword(credentialsId: "dockerhub", passwordVariable: "dockerhubpass", usernameVariable: "dockerhubuser")]) {
                    sh '''
                    docker-compose down || true    # Stop previous containers if running
                    docker-compose pull            # Pull latest image from Docker Hub
                    docker-compose up -d            # Run containers in detached mode
                '''
                }
            }
        }
    }
}
