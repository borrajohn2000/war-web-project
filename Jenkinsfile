pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "borrajohn/war-web-project"
        DOCKER_TAG = "latest"
    }
    stages {
        stage('Git Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/master']],
                    extensions: [],
                    userRemoteConfigs: [[
                        credentialsId: 'GitCreds',
                        url: 'https://github.com/borrajohn2000/war-web-project.git'
                    ]]
                )
            }
        }

        stage('Build Maven WAR') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                    # Build a Docker image
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'DockerCred', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh """
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        """
                    }
                }
            }
        }
        stage('running conatiner'){
            steps{
                 sh 'docker rm -f warappcont || true'
                sh 'docker run -itd --name warappcont -p 8091:8080 $DOCKER_IMAGE'
            }
        }
         stage('Test K8s Connection') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh 'kubectl get nodes'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    sh '''
                    kubectl apply -f k8s/Deployment.yml
                    kubectl apply -f k8s/Service.yml
                    '''
                }
            }
        }
    }
}
