pipeline {
  agent any
  stages {
    stage('CFT Stack') {
      parallel {
        stage('CFT Stack') {
          steps {
            addInfoBadge 'Create CloudFormation Stack'
          }
        }
        stage('canada-controllerHA') {
          steps {
            build(job: 'canada-controllerHA', propagate: true, wait: true)
          }
        }
      }
    }
    stage('Test1') {
      parallel {
        stage('Test Before Controller STOP') {
          steps {
            addInfoBadge 'Check Aviatrix Controller'
          }
        }
        stage('fullcheck-before-upgrade') {
          steps {
            build(job: 'fullcheck-before-upgrade', propagate: true, wait: true)
          }
        }
      }
    }
    stage('Test2') {
      steps {
        addInfoBadge 'Stop Controller EC2 Instance'
      }
    }
    stage('Report') {
      parallel {
        stage('Report') {
          steps {
            emailext(subject: '$JOB_NAME', body: 'Hello', to: 'edsel@aviatrix.com')
          }
        }
        stage('slack') {
          steps {
            slackSend(failOnError: true, message: 'ControllerHA Status', channel: '#sitdown', teamDomain: 'aviatrix', token: 'zjC6JXcuigU1Nq0j3AoLBdci', baseUrl: 'https://aviatrix.slack.com/services/hooks/jenkins-ci/')
          }
        }
      }
    }
  }
}