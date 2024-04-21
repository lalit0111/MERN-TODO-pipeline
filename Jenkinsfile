pipeline {

    agent {
        label 'jenkins-agent'
    }
    
    environment {
        DOCKER_HUB_USERNAME = 'lalit0111'
        DOCKER_HUB_PASSWORD = credentials('docker-cred')
        BACKEND_IMAGE = 'todo-node:latest'
        FRONTEND_IMAGE = 'todo-react:latest'
        K8S_MASTER_IP = '172.31.47.118'
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
                    sh "docker build -t ${DOCKER_HUB_USERNAME}/${BACKEND_IMAGE} -f backend/Dockerfile backend"
                    sh "docker build -t ${DOCKER_HUB_USERNAME}/${FRONTEND_IMAGE} -f frontend/Dockerfile frontend"
                }
            }
        }

        stage('Login and push images') {
            steps {
                script {
                    sh "docker login -u ${DOCKER_HUB_USERNAME} -p ${DOCKER_HUB_PASSWORD}"
                    sh "docker push ${DOCKER_HUB_USERNAME}/${BACKEND_IMAGE}"
                    sh "docker push ${DOCKER_HUB_USERNAME}/${FRONTEND_IMAGE}"
                }
            }
        }

        stage('Setup kubernetes using ansible') {
            steps {
                script {
                    sh "cd ansible; ansible-playbook -i inventory/k8.yml playbooks/bootstrap.yml"
                }
            }
        }

        stage('Start pods, deployments and services') {
            steps {
                script {
                    sh "sshpass -p 'ubuntu' scp -o StrictHostKeyChecking=no k8s-multi-stage.yml ubuntu@${K8S_MASTER_IP}:~/"
                    sh "sshpass -p 'ubuntu' ssh -o StrictHostKeyChecking=no ubuntu@${K8S_MASTER_IP} 'sudo kubectl apply -f ~/k8s-multi-stage.yml'"
                }
            }
        }

    }
}
