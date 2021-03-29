pipeline {
  agent any
  stages {
    stage('Build') {
      post {
        failure {
          mail(subject: 'Build status', body: 'The build failed!', to: 'ht_loucif@esi.dz')
        }

      }
      steps {
        withGradle() {
          bat 'gradle build'
          bat 'gradle javadoc'
        }

        archiveArtifacts 'build/libs/*.jar'
        junit(testResults: 'build/test-results/**/*.xml', allowEmptyResults: true)
        bat 'java -version'
      }
    }

    stage('Mail Notification') {
      steps {
        mail(subject: 'Build status', body: 'The build was successful', to: 'ht_loucif@esi.dz')
      }
    }

    stage('Code analysis') {
      parallel {
        stage('Code analysis') {
          steps {
            withSonarQubeEnv('sonar') {
              bat 'gradle sonarqube'
            }

            timeout(time: 1, unit: 'MINUTES') {
              waitForQualityGate true
            }

          }
        }

        stage('Test Reporting') {
          steps {
            cucumber '*/.json'
          }
        }

      }
    }

    stage('Deployment') {
      steps {
        withGradle() {
          bat 'C:/Users/Hamou/Downloads/gradle-6.8.3-all/gradle-6.8.3/bin/gradle publish'
        }

      }
    }

    stage('Slack Notification') {
      steps {
        slackSend(baseUrl: 'https://hooks.slack.com/services/', token: 'T01T25MKAF3/B01SPH828LS/daU7HRNCxuinpEiPQ4LLH2k8', teamDomain: 'esi-qvj8487.slack.com', channel: '#tp-jenkins', message: 'Build and Deploy Success')
      }
    }

  }
  parameters {
    string(name: 'email', defaultValue: 'Build succeeded', description: '')
  }
}