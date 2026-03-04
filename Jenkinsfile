pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "10.2.2.141"      // your server IP
        DEPLOY_USER   = "ubuntu"          // server username
        APP_PATH      = "/home/ubuntu/app.jar" // destination path on server
        SSH_CREDENTIALS = "10.2.2.141"    // Jenkins SSH credential ID
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying application to ${DEPLOY_SERVER}..."
                sshagent([env.SSH_CREDENTIALS]) {
                    sh """
                        scp -o StrictHostKeyChecking=no target/*.jar ${DEPLOY_USER}@${DEPLOY_SERVER}:${APP_PATH}
                        ssh ${DEPLOY_USER}@${DEPLOY_SERVER} "pkill java || true"
                        ssh ${DEPLOY_USER}@${DEPLOY_SERVER} "nohup java -jar ${APP_PATH} > output.log 2>&1 &"
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check logs for details."
        }
    }
}
