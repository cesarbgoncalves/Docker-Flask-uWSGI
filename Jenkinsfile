pipeline{
    agent any

    stages {
        stage("checkout scm") {
            steps {
                // Pega o commit id para ser usado de tag (versionamento) na imagem
                sh 'git rev-parse --short HEAD > commit-id'
                tag = readFile('commit-id').replace("\n", "").replace("\r", "")
            
                // configura o nome da aplicação, o endereço do repositório e o nome da imagem com a versão
                appName = "app"
                registryHost = "cesarbgoncalves/"
                imageName = "${registryHost}${appName}:${env.BUILD_NUMBER}"
            
                // Variaveis
                environment {
                    DOCKERHUB_CREDENTIALS=credentials('docherhub')
                }
            }
        }
        
        stage("Build") {
            steps {
                def customImage = docker.build("${imageName}")
            }
        }        
        
        stage("Login") {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin '
            }            
        }
        
        stage("Push") {
            steps {
                customImage.push()
            }
        }

        stage("Deploy PROD") {
            steps {
                input "Deploy to PROD?"
                customImage.push('latest')
                sh "kubectl apply -f https://raw.githubusercontent.com/cirolini/Docker-Flask-uWSGI/master/k8s_app.yaml"
                sh "kubectl set image deployment app app=${imageName} --record"
                sh "kubectl rollout status deployment/app"
            }            
        }
    }
}