pipeline {

    agent {
        label 'jenkins-agent'
    }

    tools {
        nodejs 'Node21'
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout') {
            steps {
                // Use the provided Git credentials for authentication
                script {
                    git branch: 'main', url: 'https://github.com/lalit0111/MERN-TODO-pipeline.git', credentialsId: 'github-token'
                }
            }
        }

        stage('Build docker images') {
            steps {
                script {
                    sh 'docker build -t todo-node -f backend/Dockerfile backend'
                    sh 'docker build -t todo-react -f frontend/Dockerfile frontend'
                }
            }
        }
    }
}
