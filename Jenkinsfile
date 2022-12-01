pipeline{
    
agent any
environment {
    
    VERSION = "${env.BUILD_ID}"
}


    
stages{
      
    stage('sonar quality check'){

        agent{

            docker{

                image: 'maven'
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
                     docker build -t 18.221.90.188:8083/springapp:${VERSION} .
                     docker login -u admin -p $nexus_cred 18.221.90.188:8083
                     docker push 18.221.90.188:8083/springapp:${VERSION}
                     docker rmi 18.221.90.188:8083/springapp:${VERSION}

                  '''
                       }
                }           
             } 
        }     
     }    
}