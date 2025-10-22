pipeline {
  agent any
  tools {
    jdk 'jdk17'
    maven 'Maven-3.9'
  }
  options { timestamps() }

  triggers {
    pollSCM('@daily')   // en secours si le webhook échoue
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests=false clean package'
      }
      post {
        always {
          junit '**/target/surefire-reports/*.xml'
          archiveArtifacts artifacts: 'target/*.war, target/*.jar', fingerprint: true
        }
      }
    }

    stage('SonarQube Analysis') {
      environment {
        SONAR_TOKEN = credentials('sonar-token')
      }
      steps {
        withSonarQubeEnv('My-SonarQube') {
          sh '''
            mvn -B sonar:sonar \
              -Dsonar.projectKey=<ton-project-key> \
              -Dsonar.host.url=<http://sonar-host:9000> \
              -Dsonar.login=$SONAR_TOKEN
          '''
        }
      }
    }
  }
}

