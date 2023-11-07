pipeline {

  environment {
    dockerimagename = "dzikleshop/shopfront"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/Dzikle/docker-Java-kubernetes-project.git'
      }
    }

    stage('Maven Build') {
            steps {
                script {
                    // Run Maven build
                    sh 'mvn clean package'
                }
            }
        }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        script {
          kubernetesDeploy(configs: "shopfront-service.yaml", kubeconfigId: "kubernetes")
        }
      }
    }

  }

}
