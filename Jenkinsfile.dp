pipeline {
    agent none
    stages {
        stage('Test') {
        agent {docker 'docker:latest'}
            steps {
                sh 'docker -v'
            }
        }
    }
}