
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
                     sh "scp -r -o StrictHostKeyChecking=no travis.yml ubuntu@15.207.108.63:/home/ubuntu/deployments"
                      script{
                         try{
                             sh "ssh ubuntu@15.207.108.63 kubectl apply -f /home/ubuntu/deployments/travis.yml"
                         }
                         catch(error){
                             sh "ssh ubuntu@15.207.108.63 kubectl create -f /home/ubuntu/deployments/travis.yml"
                         }
                    }
                }
            }
        }    
  }
}
