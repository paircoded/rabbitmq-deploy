def config = [
    terraform_dir: './terraform',
]

def app;

pipeline {
    environment {
        BUILD_NUMBER=/${env.BUILD_NUMBER}/
        TERRAFORM_DIR=/${config.terraform_dir}/
    }

    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Helm init') {
            steps {
                script {
                   withCredentials([file(credentialsId: 'k8sKubeConfig', variable: 'secretFile')]) {
                        sh '''
                            # god help me if I ever do anything concurrently
                            rm -rf ~/.kube && mkdir ~/.kube && chmod 700 ~/.kube && rm -f ~/.kube/config && ln -s ${secretFile} ~/.kube/config
                            helm repo add bitnami https://charts.bitnami.com/bitnami
                        '''
                    }
                }
            }
        }

        stage('Helm install') {
            steps {
               withCredentials([file(credentialsId: 'k8sKubeConfig', variable: 'secretFile')]) {
                    sh '''
                        # god help me if I ever do anything concurrently
                        rm -rf ~/.kube && mkdir ~/.kube && chmod 700 ~/.kube && rm -f ~/.kube/config && ln -s ${secretFile} ~/.kube/config
                        helm upgrade -f values.yaml rabbitmq bitnami/rabbitmq-cluster-operator --namespace paircoded
                       '''
               }
            }
        }
    }
}
