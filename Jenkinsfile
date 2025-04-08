pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')  // From Jenkins credentials
        IMAGE_NAME = "flask-gitops"
        DOCKERHUB_USERNAME = "ibrahimdarghouthi1919"
    }

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/<your_github_user>/<your_repo>.git'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t $DOCKERHUB_USERNAME/$IMAGE_NAME:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                    sh 'docker push $DOCKERHUB_USERNAME/$IMAGE_NAME:latest'
                }
            }
        }

        stage('Update Kubernetes Manifest') {
            steps {
                sh '''
                    sed -i "s|<DOCKER_USERNAME>|$DOCKERHUB_USERNAME|g" k8s/deployment.yaml
                    git config --global user.email "jenkins@example.com"
                    git config --global user.name "Jenkins"
                    git add k8s/deployment.yaml
                    git commit -m "Update image tag"
                    git push
                '''
            }
        }
    }
}
