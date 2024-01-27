pipeline {
  agent {
    label "maven"
  }
  stages {
    stage('checkout-scm') {
      steps{
        git branch: 'main', url: 'https://github.com/devopsgagan/javawebapp.git'
      }}
    stage('Build') {
      steps{
        sh 'mvn clean install'
      }
    }
    stage('sonarqube') {
          steps{
            script {
              def scannerHome = tool 'sonar-scanner';
              withSonarQubeEnv('sonarqube-server') {
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey='demo' -Dsonar.projectName='demo' -Dsonar.projectVersion='1.0'"
              }
            }
          }
      }

    stage('Deploy') {
      steps{
        sh 'echo "Here we deploy the build"'
      }
    }
  }
}
