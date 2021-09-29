node{
      def dockerImageName= 'ankitwaghela09/javadedockerapp_$JOB_NAME:$BUILD_NUMBER'
      stage('SCM Checkout'){
         git 'https://github.com/ankitwaghela09/java-groovy-docker'
      }
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Maven 3.8.2', type: 'maven'   
         sh "${mvnHome}/bin/mvn package -Dmaven.test.skip=true"
      }       
     
     stage ('Test'){
         def mvnHome =  tool name: 'Maven 3.8.2', type: 'maven'    
         sh "${mvnHome}/bin/mvn verify; sleep 3"
      }
      
     stage('Build Docker Image'){         
           sh "docker build -t ${dockerImageName} ."
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'dockerpwdankitwaghela09', variable: 'dockerPWD')]) {
              sh "docker login -u ankitwaghela09 -p ${dockerPWD}"
         }
        sh "docker push ${dockerImageName}"
      }
      
    stage('Run Docker Image'){
            def dockerContainerName = 'javadockerapp_$JOB_NAME_$BUILD_NUMBER'
            def changingPermission='sudo chmod +x stopscript.sh'
            def scriptRunner='sudo ./stopscript.sh'           
            def dockerRun= "sudo docker run -p 8082:8080 -d --name ${dockerContainerName} ${dockerImageName}" 
            withCredentials([string(credentialsId: 'deploymentserverpwd', variable: 'dpPWD')]) {
                  sh "sshpass -p ${dpPWD} ssh -T -o StrictHostKeyChecking=no netadmin@40.88.22.236" 
                  sh "sshpass -p ${dpPWD} scp -r stopscript.sh netadmin@40.88.22.236:/home/netadmin" 
                  sh "sshpass -p ${dpPWD} ssh -T -o StrictHostKeyChecking=no netadmin@40.88.22.236 ${changingPermission}"
                  sh "sshpass -p ${dpPWD} ssh -T -o StrictHostKeyChecking=no netadmin@40.88.22.236 ${scriptRunner}"
                  sh "sshpass -p ${dpPWD} ssh -T -o StrictHostKeyChecking=no netadmin@40.88.22.236 ${dockerRun}"
            }
            
      
      }
      
         
  }
      
