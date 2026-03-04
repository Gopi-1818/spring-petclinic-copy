pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no target/*.jar ubuntu@34.230.73.6:/home/ubuntu/app.jar
                    ssh ubuntu@34.230.73.6 "pkill java || true"
                    ssh ubuntu@34.230.73.6 "nohup java -jar /home/ubuntu/app.jar > output.log 2>&1 &"
                    '''
                }
            }
        }
    }
}
