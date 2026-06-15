pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        S3_BUCKET = 'frontend05082026'
        CLOUDFRONT_DISTRIBUTION_ID = 'E3570QC9A1B3OG'
    }

    stages {
        stage('Clone Frontend Repo') {
            steps {
                git branch: 'master', 
                credentialsId: 'Github-credentials', 
                url: 'https://github.com/umapathy1729/sample-devops-frontend-project.git'
            }
        }

        stage('Install & Build') {
            steps {
                sh '''
                
                rm -rf node_modules package-lock.json

                npm install --legacy-peer-deps || true

                npm run build
                
                '''
            }
        }

        stage('Deploy to S3') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'secret',  secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                    aws s3 sync ./out s3://$S3_BUCKET --delete
                    '''
                }
            }
        }

        stage('Invalidate CloudFront') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'secret', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                    aws cloudfront create-invalidation \
                      --distribution-id $CLOUDFRONT_DISTRIBUTION_ID \
                      --paths "/*"
                    '''
                }
            }
        }
    }
}
