pipeline {
    agent any

    environment {
        IMAGE = 'your-dockerhub-username/devops-app'
        TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your main branch
                git branch: 'main', url: 'https://github.com/71762333005-dev/devops-app.git'
            }
        }

        stage('SonarQube Scan') {
            steps {
                script {
                    // Assuming SonarQube is already configured in Jenkins
                    withSonarQubeEnv('SonarQube-Server') {
                        sh 'sonar-scanner'
                    }
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
                sh '''
                    git clone https://github.com/71762333005-dev/devops-gitops.git
                    cd devops-gitops
                    cp ../deployment.yaml .
                    git add deployment.yaml
                    git commit -m "Update deployment manifest"
                    git push origin main
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}
