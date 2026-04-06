pipeline {
    agent any
    environment {
        PATH = "/opt/sonar-scanner/bin:${env.PATH}" // Add SonarScanner
        IMAGE = "yourdockerhubusername/devops-app"
        TAG = "latest"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/71762333005-dev/devops-app.git'
            }
        }
        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh 'sonar-scanner'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
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
                withDockerRegistry([credentialsId: 'dockerhub-cred', url: 'https://index.docker.io/v1/']) {
                    sh 'docker push $IMAGE:$TAG'
                }
            }
        }
        stage('Update GitOps Repo') {
            steps {
                dir('devops-gitops') {
                    git branch: 'main', url: 'https://github.com/71762333005-dev/devops-gitops.git'
                    sh "cp k8s-deployment.yaml ./"
                    sh "git add . && git commit -m 'Update deployment manifest' || echo 'Nothing to commit'"
                    sh "git push origin main"
                }
            }
        }
    }
    post {
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
