pipeline {
    agent any

    environment {
        APP_SERVER = "10.2.2.141"
        DEPLOY_USER = "ubuntu"
        ARTIFACT = "spring-petclinic-4.0.0-SNAPSHOT.jar"
        MAVEN_OPTS = "-Xmx512m"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Gopi-1818/spring-petclinic-copy.git',
                    branch: 'main'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean test'
            }
        }

        stage('Security Scan') {
            steps {
                sh 'mvn org.owasp:dependency-check-maven:check -DfailBuildOnCVSS=7'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
                archiveArtifacts artifacts: "target/${ARTIFACT}", fingerprint: true
            }
        }

        stage('Deploy to Application Server') {
            when {
                branch 'main'
            }
            steps {
                sshagent(['10.2.2.141']) {
                    sh """
                        scp -o StrictHostKeyChecking=no target/${ARTIFACT} ${DEPLOY_USER}@${APP_SERVER}:/home/ubuntu/app.jar
                        ssh ${DEPLOY_USER}@${APP_SERVER} 'pkill java || true'
                        ssh ${DEPLOY_USER}@${APP_SERVER} 'nohup java -jar /home/ubuntu/app.jar > app.log 2>&1 &'
                    """
                }
            }
        }
    }
}
