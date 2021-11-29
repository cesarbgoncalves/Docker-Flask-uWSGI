node{

    checkout scm

    // Variaveis
    environment {
        DOCKERHUB_CREDENTIALS=credentials('docherhub')
                }
    sh 'git rev-parse --short HEAD > commit-id'
    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
            
    // configura o nome da aplicação, o endereço do repositório e o nome da imagem com a versão
    appName = "app"
    registryHost = "cesarbgoncalves/"
    imageName = "${registryHost}${appName}:${env.BUILD_NUMBER}"

    stage "Build"
        def customImage = docker.build("${imageName}")
    
    stage "Login"
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin '
    
    stage "Push"
        customImage.push()
    
    stage "Deploy PROD"

        input "Deploy to PROD?"
        customImage.push('latest')
        sh "kubectl apply -f https://raw.githubusercontent.com/cirolini/Docker-Flask-uWSGI/master/k8s_app.yaml"
        sh "kubectl set image deployment app app=${imageName} --record"
        sh "kubectl rollout status deployment/app"
        
}