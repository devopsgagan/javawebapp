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
            def jdkHome = tool 'JDK8'
            env.PATH = "${jdkHome}/bin:${env.PATH}"

            // Set the SonarQube scanner tool
            def scannerHome = tool 'sonar-scanner'

            // Execute SonarQube scanner with Java 8
            withSonarQubeEnv('sonarqube-server') {
                sh """
                    ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey='demo' \
                    -Dsonar.projectName='demo' \
                    -Dsonar.projectVersion='1.0' \
                    -Dsonar.java.binaries='target/classes'
                """.stripIndent()
            }
        }
    }
}
    stage("Sonar Quality Gate Check") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    script {
                        def qualityGate = waitForQualityGate()
                        if (qualityGate.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qualityGate.status}"
                        }
                    }
                } // End of timeout
            }
        }
    stage('Deploy to Nexus') {
            steps {
                nexusArtifactUploader([
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '54.89.184.200:8081', // Update with your Nexus URL
                    groupId: 'com.maven',
                    version: '9.1.14',
                    repository: 'javawebapp-nexus-repo',
                    credentialsId: "${nexus-creds-2}", // Update with your Nexus credentials ID
                    artifacts: [
                        [artifactId: 'SimpleWebApplication',
                         file: 'target/SimpleWebApplication.war',
                         type: 'war']
                    ]
                ])
            }
        }
    stage('Deploy') {
      steps{
        sh 'echo "Here we deploy the build"'
      }
    }
  }
}
