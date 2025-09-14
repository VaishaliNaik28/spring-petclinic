pipeline {
  agent any
  tools {
    maven 'M3'    // Name of Maven tool from Jenkins global config
    jdk 'JDK11'   // Name of JDK tool from Jenkins global config
  }
  environment {
    APP_NAME = 'myapp'            // Change to your app name
    TOMCAT_URL = 'http://localhost:8080'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'mvn clean package -DskipTests'
      }
    }
    stage('Archive') {
      steps {
        archiveArtifacts artifacts: 'target/*.war', fingerprint: true
      }
    }
    stage('Deploy to Tomcat') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'tomcat-creds', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASS')]) {
          sh '''
            WAR_FILE=$(ls target/*.war | head -n 1)
            curl -u $TOMCAT_USER:$TOMCAT_PASS --upload-file $WAR_FILE "$TOMCAT_URL/manager/text/deploy?path=/${APP_NAME}&update=true"
          '''
        }
      }
    }
  }
}
