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
                nexusArtifactUploader artifacts: [[artifactId: 'SimpleWebApplication', classifier: '', file: 'target/SimpleWebApplication.war', type: '*.war']], credentialsId: 'nexus-creds', groupId: 'com.maven', nexusUrl: '172.31.85.80:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'javawebapp-nexus-repo', version: '1.0.0-SNAPSHOT'
            }
        }
    stage('Deploy') {
      agent {
        label "tomcat"
      }
      steps{
        script {
          def tomcatWebappsPath = '/opt/tomcat/webapps'
          def warFileName = 'SimpleWebApplication.war'
          def tmpFolder = '/tmp'
          // Check if the existing WAR file is present
            def existingWarPath = sh(
                script: "[ -e ${tomcatWebappsPath}/${warFileName} ] && echo 'true' || echo 'false'",
                returnStatus: true
            )
            if (existingWarPath == 'true') {
                // Move existing WAR file to tmp folder
                sh "mv ${tomcatWebappsPath}/${warFileName} ${tmpFolder}/"
                echo "Existing WAR file moved to ${tmpFolder}/"
            }
          sh '''
            systemctl stop tomcat 
            // Copy the new WAR file to Tomcat
            sh "cp target/${warFileName} ${tomcatWebappsPath}/"
            systemctl start tomcat 
            
          '''
        }
      }
    }
  }
}
