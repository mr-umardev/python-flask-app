pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Installing dependencies..."
                bat '''
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running unit tests..."
                bat '''
                    python -m unittest discover -s .
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying application..."
                bat '''
                    if not exist python-app-deploy mkdir python-app-deploy
                    copy /Y app.py python-app-deploy\\
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo "Starting Flask application..."
                bat '''
                    taskkill /IM python.exe /F >nul 2>&1
                    start "" /B python python-app-deploy\\app.py
                    ping 127.0.0.1 -n 5 >nul
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo "Testing running Flask app..."
                bat '''
                    curl http://localhost:5000
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. See console logs."
        }
    }
}
