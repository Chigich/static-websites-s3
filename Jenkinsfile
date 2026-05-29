pipeline {
    agent any
    environment {
        AWS_REGION = 'us-east-1'
        S3_BUCKET  = 'chirag-static'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Chigich/static-websites-s3.git',
                    credentialsId: 'git-credentials'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install && npm run build'
            }
        }
        stage('Deploy') {
            steps {
                withCredentials([
                    string(credentialsId: 'AWS_ACCESS_KEY_ID',     variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'AWS_SECRET_ACCESS_KEY', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh """
                        aws configure set aws_access_key_id \$aws-access-key-id
                        aws configure set aws_secret_access_key \$aws-secret-access-key
                        aws configure set region \${AWS_REGION}
                        aws s3 sync dist/ s3://chirag-static/ --delete --acl public-read
                    """
                }
            }
        }
    }
    post {
        success { echo "Deployed to s3://$S3_BUCKET/" }
        failure { echo 'Build Failed!' }
        always  { cleanWs() }
    }
}
