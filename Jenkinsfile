pipeline {
    agent any

    environment {
        APP_SERVER = "10.2.2.141"
        DEPLOY_USER = "ubuntu"
        ARTIFACT_NAME = "spring-petclinic-4.0.0-SNAPSHOT.jar"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Gopi-1818/spring-petclinic-copy.git', branch: 'main', credentialsId: 'ec3f13e9-4e88-49c0-b392-c7feef5b0ac3'
            }
        }

        stage('Build & Test') {
            steps {
                echo "Running Maven build and tests..."
                sh 'mvn clean test'
            }
        }

        stage('Security Scan') {
            steps {
                echo "Running OWASP Dependency Check..."
                sh '''
                    mvn org.owasp:dependency-check-maven:check -DfailBuildOnCVSS=7
                '''
            }
        }

        stage('Package') {
            steps {
                echo "Packaging the application..."
                sh 'mvn clean package'
                archiveArtifacts artifacts: "target/${ARTIFACT_NAME}", fingerprint: true
            }
        }

        stage('Deploy to Application Server') {
            when {
                branch 'main'
            }
            steps {
                echo "Deploying application to ${APP_SERVER}..."
                sshagent(['ubuntu']) {
                    sh """
                        scp -o StrictHostKeyChecking=no target/${ARTIFACT_NAME} ${DEPLOY_USER}@${APP_SERVER}:/home/ubuntu/app.jar
                        ssh ${DEPLOY_USER}@${APP_SERVER} 'pkill java || true'
                        ssh ${DEPLOY_USER}@${APP_SERVER} 'nohup java -jar /home/ubuntu/app.jar > output.log 2>&1 &'
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for errors.'
        }
    }
}
