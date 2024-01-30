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
      steps {
        script {
            // Set the JDK to Java 8 using the 'tools' directive
            def jdkHome = tool 'java8'
            env.PATH = "${jdkHome}/bin:${env.PATH}"

            // Set the SonarQube scanner tool
            def scannerHome = tool 'sonar-scanner'

            // Execute SonarQube scanner with Java 8
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
