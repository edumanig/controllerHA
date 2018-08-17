pipeline {
  agent any
  stages {
    stage('CFT Stack') {
      parallel {
        stage('CFT Stack') {
          steps {
            addInfoBadge 'Create CloudFormation Stack'
            slackSend(message: 'Create CFT controllerHA stack', baseUrl: ' https://aviatrix.slack.com/services/hooks/jenkins-ci/', channel: '#sitdown', failOnError: true, teamDomain: 'aviatrix', token: 'zjC6JXcuigU1Nq0j3AoLBdc')
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
            emailext(subject: '$JOB_NAME. - Passed!', body: 'Hello', to: 'edsel@aviatrix.com')
          }
        }
        stage('slack') {
          steps {
            slackSend(failOnError: true, message: 'ControllerHA Status', channel: '#sitdown', teamDomain: 'aviatrix', token: 'zjC6JXcuigU1Nq0j3AoLBdci', baseUrl: 'https://aviatrix.slack.com/services/hooks/jenkins-ci/')
          }
        }
        stage('destroy') {
          steps {
            build(job: 'canada-controllerHA-stack-destroy', propagate: true, wait: true)
            slackSend(message: 'Destroy CFT controllerHA stack', baseUrl: 'https://aviatrix.slack.com/services/hooks/jenkins-ci/', channel: '#sitdown', teamDomain: 'aviatrix', failOnError: true, token: 'zjC6JXcuigU1Nq0j3AoLBdci')
          }
        }
      }
    }
    stage('Thank You') {
      steps {
        addBadge(icon: 'TY', text: 'Thank you !')
      }
    }
  }
}