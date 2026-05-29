pipeline {
 
   agent any 
 
   environment {
      AWS_ACCESS_KEY_ID   = credentials('aws-access-key-id')
      AWS_SECRET_KEY_ID   = credentials('aws-secret-access-key')
      AWS_REGION          = 'us-east-1'
      S3_BUCKET           = 'chirag-static'
   }

   stages {
    
      stage ('checkout') {
           
        stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Chigich/static-websites-s3.git',
                    credentialsId: 'git-credentials' 
            }
        }
 
        stage ('Build') {
           
            steps { 
                 sh 'npm ci && npn run build'
            }
        }

          stage('Deploy') {
            steps {
                sh '''
                    aws s3 sync dist/ s3://$S3_BUCKET/ \
                        --region $AWS_REGION \
                        --delete \
                        --cache-control "no-cache"
                '''
            }
        }
    }
 
    post {
        success { echo "Deployed to s3://$S3_BUCKET/" }
        failure { echo "Build failed — check ${env.BUILD_URL}" }
    }
}
 
             


