Project:

pipeline {
    agent any
    environment {
        SCANNER_HOME = tool 'SonarScanner'
        IMAGE_NAME   = 'myimage'
        PROJECT      = 'myproject'
        HARBOR_URL   = 'localhost'
        APP_NAME     = 'front_end_demo'
    }
 
    stages {
        stage('checkout') {
            steps {
               git branch: 'master', 
               credentialsId: 'Github-credentials', 
               url: 'https://github.com/umapathy1729/sample-devops-frontend-project.git'
            }
        }
        
        stage('SonarQube analysis') {
            steps {
               withSonarQubeEnv('SonarQube') {
                   sh ''' 
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=${APP_NAME} \
                    -Dsonar.projectName=${APP_NAME} \
                    -Dsonar.sources=src/ \
                   '''
                }
            }
        }
    
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        
        stage('install dependecies') {
            steps {
               sh "npm install"
            }
        }
        
        stage('build') {
            steps {
               sh "npm run build"
            }
        }
        
        stage('build image') {
            steps {
               sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }
        
        stage('tag image') {
            steps {
                sh """
                   docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${HARBOR_URL}/${PROJECT}/${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }             
        }
        
        stage('harbor login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'harbor_creds', 
                    passwordVariable: 'HARBOR_PASS', 
                    usernameVariable: 'HARBOR_USER')]) {
                    
                    // Added http:// explicitly right here to force HTTP instead of HTTPS
                    sh '''
                        echo "$HARBOR_PASS" | docker login http://${HARBOR_URL} \
                        -u "$HARBOR_USER" --password-stdin
                    ''' 
                } 
            }
        }
        
        stage('Push Image') {
            steps {
                sh """
                   docker push ${HARBOR_URL}/${PROJECT}/${IMAGE_NAME}:${BUILD_NUMBER}
                """
            }             
        }
    }
}


