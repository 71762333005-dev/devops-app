environment {
    PATH = "/opt/sonar-scanner/bin:${env.PATH}"
    IMAGE = "yourdockerhubusername/devops-app"
    TAG = "latest"
    SONAR_AUTH_TOKEN = credentials('sonar-token-id') // replace with your Jenkins secret ID
}

stages {
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
}
