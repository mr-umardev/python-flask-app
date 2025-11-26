pipeline {
    agent any

    stages {

        stage('Build Docker Image') {
            steps {
                bat '''
                    docker build -t python-flask-app .
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                bat '''
                    docker stop python-flask-app 2>NUL || true
                    docker rm python-flask-app 2>NUL || true
                    docker run -d -p 5000:5000 --name python-flask-app python-flask-app
                '''
            }
        }
    }
}
