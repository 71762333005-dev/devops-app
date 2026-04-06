pipeline {
    agent any

    environment {
        IMAGE = "71762333005-dev/test-app"
        TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/71762333005-dev/devops-app.git'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh 'sonar-scanner'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Build Docker') {
            steps {
                sh 'docker build -t $IMAGE:$TAG .'
            }
        }

        stage('Push Docker') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-cred']) {
                    sh 'docker push $IMAGE:$TAG'
                }
            }
        }

        stage('Update GitOps Repo') {
            steps {
                sh '''
                git clone https://github.com/71762333005-dev/devops-gitops.git
                cd devops-gitops
                sed -i "s|image:.*|image: $IMAGE:$TAG|" deployment.yaml
                git config user.email "jenkins@example.com"
                git config user.name "jenkins"
                git commit -am "Update image $TAG"
                git push
                '''
            }
        }
    }
}
