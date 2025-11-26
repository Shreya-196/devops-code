pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo 'Creating virtual environment and installing dependencies...'
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh '''
                    . venv/bin/activate
                    python3 -m unittest discover -s .
                '''
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh 'echo "Application deployed successfully"'
            }
        }
        
        stage('Run Application') {
            steps {
                echo 'Starting the Flask application...'
                sh '''
                    . venv/bin/activate
                    nohup python3 app.py > app.log 2>&1 &
                    echo $! > app.pid
                    sleep 5
                    echo "Application started"
                '''
            }
        }
        
        stage('Test Application') {
            steps {
                echo 'Testing if application is running...'
                sh '''
                    curl -f http://localhost:5000 || exit 1
                    echo "Application is running successfully!"
                '''
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for more details.'
        }
        always {
            echo 'Cleaning up...'
            sh '''
                if [ -f app.pid ]; then
                    kill $(cat app.pid) 2>/dev/null || true
                    rm app.pid
                fi
            '''
        }
    }
}
