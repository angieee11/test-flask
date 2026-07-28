pipeline {
    agent any

    environment {
        VENV = 'venv'

        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = '278741242236'
        ECR_REPOSITORY = 'test-flask'

        AWS_CREDENTIALS_ID = 'e89db875-9678-4dd3-9932-779a3c1eb9ec'

        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        ECR_IMAGE = "${ECR_REGISTRY}/${ECR_REPOSITORY}"
    }

    stages {
        stage('Checkout git') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/angieee11/test-flask.git'
            }
        }

        stage('Set up the venv') {
            steps {
                sh '''
                    python3 -m venv "$VENV"
                    "$VENV/bin/pip" install -r requirements.txt
                '''
            }
        }

        stage('Run the tests') {
            steps {
                sh '"$VENV/bin/python" -m unittest discover -s tests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build -t "test-flask:$BUILD_NUMBER" .

                    docker tag "test-flask:$BUILD_NUMBER" \
                        "$ECR_IMAGE:$BUILD_NUMBER"

                    docker tag "test-flask:$BUILD_NUMBER" \
                        "$ECR_IMAGE:latest"
                '''
            }
        }

        stage('Push Image to AWS ECR') {
            steps {
                withCredentials([
                    [
                        $class: 'AmazonWebServicesCredentialsBinding',
                        credentialsId: env.AWS_CREDENTIALS_ID
                    ]
                ]) {
                    sh '''
                        aws ecr get-login-password \
                            --region "$AWS_REGION" |
                        docker login \
                            --username AWS \
                            --password-stdin "$ECR_REGISTRY"

                        docker push "$ECR_IMAGE:$BUILD_NUMBER"
                        docker push "$ECR_IMAGE:latest"

                        docker logout "$ECR_REGISTRY"
                    '''
                }
            }
        }
    }
}