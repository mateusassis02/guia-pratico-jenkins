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
            environment { // Correção aqui
                tag_version = "${env.BUILD_ID}" // Removido o parêntese extra
            }

            steps {
                // Executa comandos de deploy usando kubectl
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'sed -i "s/{{tag}}/$tag_version/g" .k8s/deployment.yaml'
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }
    }
}
