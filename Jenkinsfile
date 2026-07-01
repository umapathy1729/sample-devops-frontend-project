
pipeline {
  agent any
  environment {
    AWS_DEFAULT_REGION = "ap-south-1
    S3_BUCKET = "mybucket01172026"
    CLOUDFRONT_DISTRIBUTION_ID = "E3QE08ZUZK4YZK"
  }

  stages {
    stage('Git clone') {
      steps {
        git branch: 'master',
        credentialsId: 'Github_credentials'
        url : ''
      }
    }

    stage('Run & build') {
      steps {
        rm -rf node_modules package_lock.json
        npm install --legacy-peer-deps || true
        npm run build
      }
    }

    stage('S3_bucket') {
      steps {
        withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'secret', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
          sh '''
          aws s3 sync ./dist s3://$S3_BUCKET
          '''
        }
      }

      stage('Cloudfront') {
        steps {
          withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'secret', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
            sh '''
            aws cloudfront create-invalidation --distribution-id $CLOUDFRONT_DISTRIBUTION_ID --paths "/*"
            '''
          }
        }
      }
    }
  }
  























        
        
        
