pipeline {
    agent any

    stages {
        stage('Checkout git') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/angieee11/test-flask.git'
            }
        }

        stage('set up the venv') {
            steps {
                sh '''
                    python3 -m venv venv
                    venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('RUN THE TESTS') {
            steps {
                sh 'venv/bin/python -m unittest discover -s tests'
            }
        }
    }
}
