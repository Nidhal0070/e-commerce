pipeline {
  agent any
environment {
  MVN = 'mvn'
  PROD_HOST = '192.168.189.129'
  PROD_USER = 'serveur'
  DEPLOY_PATH = '/tmp'
  APP_URL = "http://192.168.189.129:8080/e-commerce"
 SONAR_TOKEN = credentials('sonar-token')
  SONAR_SERVER = 'SonarQube'
}
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Build & Test') {
      steps { sh "${MVN} clean test" }
      
    }
    stage('SAST - Sonar') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh "${MVN} sonar:sonar -Dsonar.login=${SONAR_TOKEN}"
        }
      }
    }
    stage('Package') {
      steps {
        sh "${MVN} -DskipTests package"
        archiveArtifacts artifacts: 'target/*.war', fingerprint: true
      }
    }
    stage('Deploy') {
      steps {
        sshagent(['jenkins-ssh-credential-id']) {
          sh """
            scp -o StrictHostKeyChecking=no target/*.war ${PROD_USER}@${PROD_HOST}:${DEPLOY_PATH}/app.war
            ssh -o StrictHostKeyChecking=no ${PROD_USER}@${PROD_HOST} 'sudo mv ${DEPLOY_PATH}/app.war /var/lib/tomcat9/webapps/ && sudo systemctl restart tomcat9'
          """
        }
      }
    }
    stage('DAST - ZAP') {
      steps {
        sh """docker run --rm owasp/zap2docker-stable zap-baseline.py -t ${APP_URL} -r zap_report.html
              mv zap_report.html ${WORKSPACE}/zap_report.html"""
        archiveArtifacts artifacts: 'zap_report.html'
      }
    }
  }
  post {
    
    success { echo "Pipeline OK" }
  }
}
