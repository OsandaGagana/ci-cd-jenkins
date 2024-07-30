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
                script {
                    def inventory = 'ansible/inventory'
                    def playbook = 'ansible/deploy.yml'
                    sh "ansible-playbook -i ${inventory} ${playbook}"
                }
            }
        }
    }
}

