pipeline { 
  environment { 
      registry = "cesarbgoncalves/app" 
      registryCredential = 'dockerhub' 
      dockerImage = ''
      imageName = 'app'
  }
  agent any 
  stages {
    //   stage('Baixando kubeconfig') {
    //       steps {
    //           sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.22.4/bin/linux/amd64/kubectl"'
    //           sh 'chmod u+x ./kubectl'
    //       }
    //   }
   
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
              sh "kubectl apply -f k8s_app.yaml --kubeconfig=/home/jenkins/.kube/config"
              sh "kubectl set image deployment app app=${imageName}:${BUILD_NUMBER} --record --kubeconfig=/home/jenkins/.kube/config"
            //   sh "kubectl -n app-python rollout status deployment/app"
                     
          }
      }
      stage('Cleaning up') {
          steps {
              sh "docker rmi $registry:$BUILD_NUMBER"
          }
      }
  }
}
