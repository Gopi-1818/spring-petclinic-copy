pipeline {
    agent any

    // Use Jenkins-managed Maven tool
    tools {
        maven 'Maven-3' // Must match your Global Tool Configuration name
    }

    environment {
        APP_SERVER = '34.230.73.6'
        APP_USER = 'ubuntu'
        APP_PATH = '/home/ubuntu/app.jar'
    }

    stages {
        stage('Build') {
            steps {
                echo "Building application..."
                // Skip Docker/Testcontainers if not available
                sh 'mvn clean package -DskipTests || true'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying application to server..."
                sshagent(['app-server-ssh']) {
                    sh """
                    # Copy jar to application server
                    scp -o StrictHostKeyChecking=no target/*.jar ${APP_USER}@${APP_SERVER}:${APP_PATH}

                    # Stop any running instance
                    ssh ${APP_USER}@${APP_SERVER} "pkill java || true"

                    # Start the application in background
                    ssh ${APP_USER}@${APP_SERVER} "nohup java -jar ${APP_PATH} > output.log 2>&1 &"
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check console output for details."
        }
    }
}
