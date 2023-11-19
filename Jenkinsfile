pipeline {
    agent any
    environment {
        PORT = "8000"
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t polthomson/lbg:${BUILD_NUMBER} --build-arg PORT=${PORT} .
                docker tag polthomson/lbg:${BUILD_NUMBER} polthomson/lbg:latest 
                docker push polthomson/lbg:${BUILD_NUMBER}
                docker push polthomson/lbg:latest
                docker rm polthomson/lbg:${BUILD_NUMBER}
                docker rm polthomson/lbg:latest
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh jenkins@polthomson-deploy <<EOF
                export PORT=${PORT}
                export VERSION=${BUILD_NUMBER}
                docker stop lbg-api && echo "stopped" || echo "not running"
                docker rm lbg-api && echo "removed" || echo "already removed"
                docker run -d -p 80:${PORT} -e PORT=${PORT} polthomson/lbg:${VERSION}
                '''
            }
        }
    }
}
