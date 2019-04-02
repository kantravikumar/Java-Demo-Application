node{
      
      stage('SCM Checkout'){
         git 'https://github.com/kantravikumar/Java-Demo-Application'
      }
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'   
         sh "${mvnHome}/bin/mvn package"
      }       
       
      stage ('Test'){
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'    
         sh "${mvnHome}/bin/mvn verify; sleep 3"
      }
       stage('Build Docker Image'){
         sh 'docker build -t ravikant1/javademoapp:$BUILD_NUMBER .'
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'dockerpwdravikant', variable: 'dockerPWDravikant')]) {
              sh "docker login -u ravikant1 -p ${dockerPWDravikant}"
         }
        sh 'docker push ravikant1/javademoapp:$BUILD_NUMBER'
        //sed 's/#BUILD-NUMBER#/$BUILD_NUMBER/' deployment.yaml
       sh "sed -i.bak 's/#BUILD-NUMBER#/$BUILD_NUMBER/' deployment.yaml"
      }
           
      stage ('copy'){    
            //def textReplace="sed 's/#BUILD-NUMBER#/$BUILD_NUMBER/' deployment.yaml"
          withCredentials([string(credentialsId: 'k8pwdravikant', variable: 'k8PWDravikant')]) {
             sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no ubuntu@104.211.154.244"  
            // sh "sshpass -p ${k8PWD} scp -r deployment.yaml ubuntu@104.211.190.132:/home/ubuntu"  
             sh "sshpass -p ${k8PWD} scp -r deployment.yaml ubuntu@104.211.190.132:/home/ubuntu" 
             //sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no ubuntu@104.211.190.132 ${textReplace}" 
          }
      }
      
      stage('Deploy'){
         def k8Apply= "kubectl apply -f deployment.yaml" 
         withCredentials([string(credentialsId: 'k8pwd', variable: 'k8PWD')]) {
            sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no ubuntu@104.211.190.132 ${k8Apply}"
         }
       }
  }
