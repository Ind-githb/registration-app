pipeline {
  agent { label 'Jenkins-Agent' }
  tools {
    jdk 'Java17'
    maven 'Maven3'
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
  }
  
}
