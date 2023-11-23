pipeline {
    agent any
    
    tools {
        maven 'M3' 
    }

    environment {
        DOCKER_IMAGE = 'dzikleshop/shopfront'
        DOCKER_TAG = 'latest'
        DOCKER_CREDENTIALS_ID = 'dockerhublogin'
        REGISTRY_URL = 'https://index.docker.io/v1/'
    }

    stages {
        stage('Git Checkout') {
            steps {
                checkout scm: [
                    $class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    userRemoteConfigs: [[url: 'https://github.com/Dzikle/docker-Java-kubernetes-project.git']]
                ]
            }
        }

        stage('Maven Build') {
            steps {
                script {
                    dir('/var/jenkins_home/workspace/test/shopfront'){
                        sh 'mvn clean package -DskipTests'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dir('/var/jenkins_home/workspace/test/shopfront'){
                        sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                        sh "echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin $REGISTRY_URL"
                    }
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                }
            }
        }

            stage('Deploy to Kubernetes') {
                steps {
        script {
          kubernetesDeploy(configs: "shopfront-service.yaml", kubeconfigId: "kubernetes")
        }
      }
            }

        }
    }
