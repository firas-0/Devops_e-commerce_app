pipeline {
  agent any

  tools {
    jdk 'JDK-17'        // ou 'JDK-21' si tu as nommé l’outil ainsi dans Jenkins
  }

  options { timestamps() }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Build & Test') {
      steps {
        dir('e-commerce-backend') {
          sh 'chmod +x mvnw || true'
          sh './mvnw -B clean verify'
        }
      }
      post {
        always {
          // Ne pas rater le build si pas (encore) de tests
          junit testResults: 'e-commerce-backend/**/surefire-reports/*.xml, e-commerce-backend/**/failsafe-reports/*.xml',
               allowEmptyResults: true
        }
      }
    }

    stage('Package') {
      steps {
        dir('e-commerce-backend') {
          sh './mvnw -B -DskipTests package'
        }
      }
      post {
        success {
          archiveArtifacts artifacts: 'e-commerce-backend/target/*.jar', fingerprint: true
        }
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('My-SonarQube') { // ← même nom que dans Jenkins
          dir('e-commerce-backend') {      // ← là où se trouve le pom.xml
            sh '''
              ./mvnw -B \
                -DskipTests=false \
                sonar:sonar \
                -Dsonar.projectKey=ecommerce-backend \
                -Dsonar.projectName=ecommerce-backend \
                -Dsonar.host.url=$SONAR_HOST_URL
            '''
          }
        }
      }
    }
  }
}

