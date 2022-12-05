pipeline{
    
agent any
environment {
    
    VERSION = "${env.BUILD_ID}"
}


    
stages{
      
    stage('sonar quality check'){

        agent{

            docker {

                image 'maven'
            }

        }
       
        steps{
            
           script{
               
               withSonarQubeEnv(credentialsId: 'sonar_api') {
                   
                  sh 'mvn clean package sonar:sonar' 
                   
                    }
               
                }
            
             } 
        }        
    stage('Quality Gate Status '){
       
        steps{
            
           script{
               
              waitForQualityGate abortPipeline: false, credentialsId: 'sonar_api'
              
                }
            
             } 
        } 

    stage('Docker Image Push '){
       
        steps{
            
           script{
                  withCredentials([string(credentialsId: 'nexus_pass', variable: 'nexus_cred')]) {
                  sh '''
                     docker build -t 3.144.143.118:8083/springapp:${VERSION} .
                     docker login -u admin -p $nexus_cred 3.144.143.118:8083
                     docker push 3.144.143.118:8083/springapp:${VERSION}
                     docker rmi 3.144.143.118:8083/springapp:${VERSION}

                  '''
                       }
                }           
             } 
        }   

     stage('Identifying misconfig using datree in helm charts '){
       
        steps{
            
           script{
                 dir('kubernetes/myapp/'){
                    withEnv(['DATREE_TOKEN=ed4c6ff1-fadd-45ad-b6b1-7337dfa087dc']) {
                         sh 'sudo helm datree test .'
                               }
                       }
                }           
             } 
        }          
     }    
}