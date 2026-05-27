pipeline {
agent any
environment {
    S3_BUCKET = 'jenkins-codedeploy-artifacts-yourname'
    APP_NAME = 'microservice-app'
    DEPLOY_GROUP = 'microservice-deploy-group'
    AWS_REGION = 'ap-south-2'
    AWS_ACCESS_KEY_ID = credentials('aws-access-key')
    AWS_SECRET_ACCESS_KEY = credentials('aws-secret-key')
}

stages {
    stage('Clone Repository') {
        steps {
            echo 'Cloning repository from GitHub...'
            checkout scm
        }
    }
    
    stage('Build') {
        steps {
            echo 'Building application artifact...'
            sh 'zip -r microservice.zip . -x "*.git*"'
        }
    }
    
    stage('Upload to S3') {
        steps {
            echo 'Uploading artifact to S3...'
            sh '''
                aws s3 cp microservice.zip s3://${S3_BUCKET}/microservice.zip --region ${AWS_REGION}
            '''
        }
    }
    
    stage('Trigger CodeDeploy') {
        steps {
            echo 'Triggering AWS CodeDeploy...'
            sh '''
                aws deploy create-deployment \
                    --application-name ${APP_NAME} \
                    --deployment-group-name ${DEPLOY_GROUP} \
                    --s3-location bucket=${S3_BUCKET},key=microservice.zip,bundleType=zip \
                    --region ${AWS_REGION}
            '''
        }
    }
    
    stage('Deployment Complete') {
        steps {
            echo 'Deployment triggered successfully!'
        }
    }
}

post {
    success {
        echo 'Pipeline completed successfully!'
    }
    failure {
        echo 'Pipeline failed. Check logs.'
    }
}
}
