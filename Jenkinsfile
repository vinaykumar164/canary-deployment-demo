pipeline {
    agent any

    environment {
        CANARY_IP = "16.112.160.17"
        PROD_IP   = "18.61.229.148"
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo 'Cloning Source Code'
                git branch: 'main',
                    url: 'https://github.com/vinaykumar164/canary-deployment-demo.git'
            }
        }

        stage('Deploy to Canary') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ubuntu@$CANARY_IP "mkdir -p ~/app"

                scp -o StrictHostKeyChecking=no app.py deploy.sh requirements.txt ubuntu@$CANARY_IP:~/app/

                ssh -o StrictHostKeyChecking=no ubuntu@$CANARY_IP '
                cd ~/app
                chmod +x deploy.sh
                ./deploy.sh
                '
                '''
            }
        }

        stage('Canary Health Check') {
            steps {
                sh '''
                sleep 10
                curl http://$CANARY_IP:5000
                '''
            }
        }

        stage('Manual Approval') {
            steps {
                input 'Approve deployment to Production?'
            }
        }

        stage('Deploy to Production') {
            steps {
                sh '''
                ssh -o StrictHostKeyChecking=no ubuntu@$PROD_IP "mkdir -p ~/app"

                scp -o StrictHostKeyChecking=no app.py deploy.sh requirements.txt ubuntu@$PROD_IP:~/app/

                ssh -o StrictHostKeyChecking=no ubuntu@$PROD_IP '
                cd ~/app
                chmod +x deploy.sh
                ./deploy.sh
                '
                '''
            }
        }

        stage('Production Health Check') {
            steps {
                sh '''
                sleep 10
                curl http://$PROD_IP:5000
                '''
            }
        }
    }
}