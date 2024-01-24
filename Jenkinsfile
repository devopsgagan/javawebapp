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
    stage('Test') {
      parallel {
        stage('Test1') {
          steps{
            sh 'echo "test case 1"'
          }
        }
        stage('Test2'){
          steps{
            sh 'echo "test case 2 "'
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
