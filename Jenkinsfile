environment {
    APP_NAME = 'new-app-nti' 
    REPO_URL = "https://github.com/YosraMamdouh/docker.git"
}

stages {
    stage('Getting Repo files') {
        steps {
            git branch: "main",  url: "${REPO_URL}"
        }
    }

    stage('Build Docker Image') {
        steps {
            script {
                sh """
                    docker build -t ${APP_NAME}:${BUILD_NUMBER} .
                """
            }
        }
    }

  stage('Push Docker Image') {
steps {
    script {
        withCredentials([
            usernamePassword(
               
                usernameVariable: 'DOCKER_USERNAME',
                passwordVariable: 'DOCKER_PASSWORD'
            )
        ]) {
            sh '''
                echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin

                docker tag new-app-nti:$BUILD_NUMBER $DOCKER_USERNAME/new-app-nti:$BUILD_NUMBER

                docker push $DOCKER_USERNAME/new-app-nti:$BUILD_NUMBER
            '''
        }
    }
}
