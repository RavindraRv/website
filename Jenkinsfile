pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-1:ravindra-capstone'
       appRegistry = "127778518722.dkr.ecr.us-east-1.amazonaws.com/capstone-ravi"
       capstoneRegistry = "127778518722.dkr.ecr.us-east-1.amazonaws.com"
       cluster = "capstone-ravi"
        service = "capstone_serv"
   }

    stages {
        stage('Clone given Website') {
            steps {
                git url:'https://github.com/RavindraRv/website.git'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test my Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload docker App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploying website to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'ravindra-capstone', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
