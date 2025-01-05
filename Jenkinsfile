pipeline {
  agent { label 'Jenkins-Agent' }
  tools {
    jdk 'Java17'
    maven 'Maven3'
  }
  environment {
    APP_NAME = "registration-app-pipeline"
    DOCKER_IMAGE = "indmyul/webapp:${BUILD_NUMBER}"
    DOCKER_PASS = 'dockerhub'
    RELEASE = "1.0.0"
    IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
  }
  stages {
      stage("Cleanup Workspace") {
        steps {
          cleanWs()
        }
      }
      stage('Checkout from SCM') {
        steps {
          sh 'echo passed'
          git branch: 'main', credentialsId: 'github', url: 'https://github.com/Ind-githb/registration-app.git'
        }
      }
      stage('Build Application') {
        steps {
          sh 'ls -ltr'
          // build the project and create a JAR file
          sh 'mvn clean package'
        }
      }
      stage('Test Application') {
        steps {
          sh 'mvn test'
        }
      }
      stage('SonarQube Analysis') {
        steps {
            script {
                withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
                    sh 'mvn sonar:sonar'
                }
            }
         }
       }
     stage('Quality Gate') {
        steps {
            script {
                waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
            }
        }
    }
    stage('Build and Push Docker Image') {
      steps {
        script {
            docker.withRegistry('', DOCKER_PASS) {
                docker_image = docker.build "${DOCKER_IMAGE}"
            }

            docker.withRegistry('', DOCKER_PASS) {
                docker_image.push("${IMAGE_TAG}")
                docker_image.push('latest')
            }
        }
      }
    }
  }
  
}
