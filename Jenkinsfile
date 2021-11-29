pipeline { 
  environment { 
      registry = "cesarbgoncalves/app" 
      registryCredential = 'dockerhub' 
      dockerImage = '' 
  }
  agent any 
  stages {
      stage('Baixando kubeconfig') {
          steps {
              sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.22.4/bin/linux/amd64/kubectl'
              sh 'chmod u+x ./kubectl'
          }
      }
   
      stage('Building our image') {
          steps {
              script {
                  dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
          } 
      }

      stage('Deploy our image') {
          steps {
              script {
                  docker.withRegistry( '', registryCredential ) {
                      dockerImage.push()
                  }
              }
          }
      } 
      stage('Deploy PROD') {
          steps {
              withKubeConfig([credentialsId: 'kubeconfig']){
                  //   customImage.push('latest')
                  sh "kubectl apply -f https://raw.githubusercontent.com/cesarbgoncalves/Docker-Flask-uWSGI/master/k8s_app.yaml"
                  sh "kubectl set image deployment app app=${imageName} --record"
                  sh "kubectl rollout status deployment/app"
              }            
          }
      }
      stage('Cleaning up') {
          steps {
              sh "docker rmi $registry:$BUILD_NUMBER"
          }
      }
  }
}
