pipeline {
    agent any
    
    environment {
        // Define environment variables if needed
        DEPLOY_SERVER = 'ec2-user@52.55.231.189'
        DEPLOY_PATH = '/home/ec2-user/target'
        SSH_KEY = credentials(aws-ssh-key)
    }
    
    stages {
        stage('Build') {
            environment {
                PATH = "/opt/maven/bin:${env.PATH}"
            }
            steps {
                script {
                    // Checkout code from version control
                    checkout scm
                    
                    // Build the project using Maven
                    sh 'mvn clean package'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    // Copy the JAR file to the remote server
                    sh """
                        scp -i ${SSH_KEY} /var/lib/jenkins/workspace/simple-test-java-pipeline/target/simple-java-app-0.0.1-SNAPSHOT.jar ${DEPLOY_SERVER}:${DEPLOY_PATH}
                    """
                    
                    // Restart the application on the remote server
                    sh """
                        ssh -i ${SSH_KEY} ${DEPLOY_SERVER} '
                            pkill -f myapp.jar || true
                            nohup java -jar ${DEPLOY_PATH}/myapp.jar > ${DEPLOY_PATH}/output.log 2>&1 &
                        '
                    """
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


