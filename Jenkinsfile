pipeline {
      agent none
      environment {
           SG_CLIENT_ID = credentials("SG_CLIENT_ID")
           SG_SECRET_KEY = credentials("SG_SECRET_KEY")

        }
  stages {
          
         stage('Clone Github repository') {
            
    
           steps {
              
             checkout scm
           
             }
  
          }
    stage('SourceGuard Code Scan') {   
          agent {
             docker  { image 'sourceguard/sourceguard-cli:latest'
                     label 'docker' }
              }  
                   
       steps {   
                   
         script {      
              try {
                    
         sh "/sourceguard-cli --src ./"
         
               } catch (Exception e) {
    
                 echo "Code Analysis is BLOCK and recommend not using the source code"  
                  }
              }
            }
         }
           
           
          stage('Docker image Build and scan prep') {
           
                steps {

              sh 'docker build -t taylen/tafridaydemo .'
              sh 'docker save taylen/tafridaydemo -o tafridaydemo.tar'
              
             } 
           }
       stage('SourceGuard Container Image Scan') {   
             agent {
               { docker 'sourceguard/sourceguard-cli' }
              }
          steps {   
                   
           
         
                    sh "/sourceguard-cli --img tafridaydemo.tar"
           
                }
             
            
            }
            
           
        stage('Publish to Docker Hub') {
           
                  steps {
                       script {
                           try {
                             docker.withRegistry('registry', 'docker_hub') {
                              dockerImage.push("${env.BUILD_NUMBER}")
                              dockerImage.push("latest")
                              }
                          } catch (Exception e) {
    
                             echo "Stage failed, but we continue"  
                             }      
                        }     
                  }
             }
    } 
}
