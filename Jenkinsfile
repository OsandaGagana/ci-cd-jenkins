pipeline {
    agent any
    
    environment {
        // Define environment variables if needed
        DEPLOY_SERVER = 'ec2-user@52.55.231.189'
        DEPLOY_PATH = '/home/ec2-user/target'
        SSH_KEY = credentials('aws-ssh-key')
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




pipeline {
    agent any

    environment {
        SSH_CREDENTIALS_ID = 'aws-ssh-key'
        REMOTE_USER = 'ec2-user'
        REMOTE_HOST = '52.55.231.189'
        REMOTE_PATH = '/var/lib/jenkins/workspace/simple-test-java-pipeline/target/simple-java-app-0.0.1-SNAPSHOT.jar'
    }

    stages {
        stage('Build') {
            steps {
                // Building the Java project and creating the .jar file
                sh 'mvn clean package'
            }
        }
        
        stage('Deploy') {
            steps {
                // Using SSH credentials
                withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY_PATH')]) {
                    script {
                        // Define the local and remote paths
                        def localJar = '/home/ec2-user/target/simple-java-app-0.0.1-SNAPSHOT.jar'
                        
                        // Transfer the .jar file using SCP
                        sh """
                            scp -i \$SSH_KEY_PATH ${localJar} ${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_PATH}
                        """
                        
                        // Optional: Run the .jar file on the remote EC2 instance
                        // sh "ssh -i \$SSH_KEY_PATH ${REMOTE_USER}@${REMOTE_HOST} 'java -jar ${REMOTE_PATH}/yourapp.jar'"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}

