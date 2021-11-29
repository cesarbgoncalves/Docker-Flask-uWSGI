pipeline { 
  environment { 
      registry = "cesarbgoncalves/app" 
      registryCredential = 'dockerhub' 
      dockerImage = '' 
  }
  agent any 
  stages { 
    //   stage('Cloning our Git') { 

    //       steps { 

    //           git 'git@github.com:cesarbgoncalves/Docker-Flask-uWSGI.git' 

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

      stage('Cleaning up') { 

          steps { 

              sh "docker rmi $registry:$BUILD_NUMBER" 

          }

      }

      stage('Deploy PROD') {

          steps {
            //   customImage.push('latest')
              sh "kubectl apply -f https://raw.githubusercontent.com/cirolini/Docker-Flask-uWSGI/master/k8s_app.yaml"
              sh "kubectl set image deployment app app=${imageName} --record"
              sh "kubectl rollout status deployment/app"
          }
      } 
  }
}
