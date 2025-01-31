
pipeline {

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git url:'https://github.com/MohankumarMarimuthu/httpbin.git', branch:'master'
      }
    }
    
      stage("Build image") {
            steps {
                script {
                    myapp = docker.build("mk1325612/httpbin:${env.BUILD_ID}")
                }
            }
        }
    
      stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'Dockerhub-Credentials') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
    stage('Deployment on k8s'){
            steps{
                sshagent(['k8s']) {
                     sh "scp -r -o StrictHostKeyChecking=no httpbin.yml ubuntu@3.110.127.197:/home/ubuntu/deployments"
                      script{
                         try{
                             sh "ssh ubuntu@3.110.127.197 kubectl apply -f /home/ubuntu/deployments/httpbin.yml"
                         }
                         catch(error){
                             sh "ssh ubuntu@3.110.127.197 kubectl create -f /home/ubuntu/deployments/httpbin.yml"
                         }
                    }
                }
            }
        }    
  }
}
