pipeline {
      agent any
      environment {
           SG_CLIENT_ID = credentials("SG_CLIENT_ID")
           SG_SECRET_KEY = credentials("SG_SECRET_KEY")
            registry = "taylen/tafridaydemo"
            registryCredential = 'dockerhub'
            dockerImage = ''

        }
  stages {         
         stage('Clone Github repository') {          
           steps {              
             checkout scm           
             }
  
          }
    stage('SourceGuard Code Scan') {   
       steps {   
                   
         script {      
              try {
                                    
                sh 'chmod +x sourceguard-cli' 

                sh './sourceguard-cli --src .'
           
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
          steps {   
                   
           
         
                    sh './sourceguard-cli --img tafridaydemo.tar'
           
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
      
 post {
    failure {
        mail to: 'taylen@checkpoint.com',
             subject: "Failed Pipeline Run: ${currentBuild.fullDisplayName}",
             body: "Something is wrong with ${env.BUILD_URL} - Go Check the pipeline to make sure there isnt a nasty blowout!"
    }
}     
      
}
