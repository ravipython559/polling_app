pipeline {
    agent any
    stages {
        stage('Build') { 
            steps {
                sh 'pip3 install -r requirements.txt'
            }
        }
        stage('Test') { 
            steps {
                sh 'python3 manage.py test'
            }
        }
        stage('Deploy to Staging') {
            when {
                branch 'staging-main' 
            } 
            steps {
                sh 'ssh -o StrictHostKeyChecking=no deployment-user@192.168.56.104 "source venv/bin/activate; \
                cd polling_app; \
                git checkout staging-main; \
                git pull origin staging-main; \
                pip install -r requirements.txt --no-warn-script-location; \
                python manage.py migrate; \
                deactivate; \
                sudo systemctl restart nginx; \
                sudo systemctl restart gunicorn"'
            }
        }
        stage('Deploy to Prod') {
            when {
                branch 'main' 
            } 
            steps {
                sh 'ssh -o StrictHostKeyChecking=no deployment-user@192.168.56.101 "source venv/bin/activate; \
                cd polling_app; \
                git pull origin main; \
                pip install -r requirements.txt --no-warn-script-location; \
                python manage.py migrate; \
                deactivate; \
                sudo systemctl restart nginx; \
                sudo systemctl restart gunicorn"'
            }
        }
    }
}