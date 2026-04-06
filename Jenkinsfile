pipeline {
    agent any

    environment {
        PATH = "/opt/sonar-scanner/bin:${env.PATH}"
        IMAGE = "yourdockerhubusername/devops-app"
        TAG = "latest"
        SONAR_AUTH_TOKEN = credentials('sonar-token-id') // replace with your Jenkins secret ID
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
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=devops-app \
                          -Dsonar.projectName="DevOps App" \
                          -Dsonar.sources=. \
                          -Dsonar.host.url=$SONAR_HOST_URL \
                          -Dsonar.login=$SONAR_AUTH_TOKEN
                    '''
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
                sh "docker build -t $IMAGE:$TAG ."
            }
        }

        stage('Push Docker') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-id', url: '']) {
                    sh "docker push $IMAGE:$TAG"
                }
            }
        }

        stage('Update GitOps Repo') {
            steps {
                echo "GitOps repo update step (implement as needed)"
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
    }
}
