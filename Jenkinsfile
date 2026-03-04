pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application to server...'
                sshagent(['10.2.2.141']) {  // <-- use your actual credential ID here
                    sh '''
                        scp -o StrictHostKeyChecking=no target/*.jar ubuntu@10.2.2.141:/home/ubuntu/app.jar
                        ssh ubuntu@10.2.2.141 "pkill java || true"
                        ssh ubuntu@10.2.2.141 "nohup java -jar /home/ubuntu/app.jar > output.log 2>&1 &"
                    '''
                }
            }
        }
    }
}
