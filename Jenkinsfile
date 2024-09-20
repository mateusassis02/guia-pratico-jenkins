pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Constrói a imagem docker utilizando o Dockerfile especificado
                    dockerapp = docker.build("mateusassis02/guia-pratico-jenkins:${env.BUILD_ID}", '-f ./src/Dockerfile')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push da imagem para o Docker Hub
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy no Kubernetes') {
            steps {
                // Executa comandos de deploy usando kubectl
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }
}
