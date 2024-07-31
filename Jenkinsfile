pipeline {
    agent any
    
    environment {
        DEPLOY_SERVER = 'ec2-user@52.55.231.189'
        DEPLOY_PATH = '/home/ec2-user/target'
    }
    
    stages {
        stage('Build') {
            steps {
                script {
                    checkout scm
                    sh 'mvn clean package'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                withCredentials([file(credentialsId: 'aws-ssh-key', variable: '/home/osanda/Downloads/test-aws.pem')]) {
                    script {
                        // Add the host key to known_hosts
                        sh 'ssh-keyscan -H ${DEPLOY_SERVER.split("@")[1]} >> ~/.ssh/known_hosts'
                        
                        // Copy the JAR file to the remote server
                        sh """
                            scp -i \$SSH_KEY_PATH target/myapp.jar ${DEPLOY_SERVER}:${DEPLOY_PATH}
                        """
                        
                        // Restart the application on the remote server
                        sh """
                            ssh -i \$SSH_KEY_PATH ${DEPLOY_SERVER} '
                                pkill -f myapp.jar || true
                                nohup java -jar ${DEPLOY_PATH}/myapp.jar > ${DEPLOY_PATH}/output.log 2>&1 &
                            '
                        """
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo 'Deployment was successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}



