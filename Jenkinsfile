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
            build(job: 'canada-controllerHA', propagate: true, wait: true, quietPeriod: 2)
          }
        }
      }
    }
    stage('Upgrade') {
      parallel {
        stage('Test Before Controller STOP') {
          steps {
            addInfoBadge 'Check Aviatrix Controller'
          }
        }
        stage('transit-upgrade') {
          steps {
            build(job: 'transit-upgrade', propagate: true, wait: true, quietPeriod: 2)
          }
        }
        stage('wait') {
          steps {
            sleep(unit: 'MINUTES', time: 5)
          }
        }
      }
    }
    stage('Stop Controller1') {
      parallel {
        stage('Trigger Controller HA') {
          steps {
            addInfoBadge 'Stop Controller EC2 Instance'
          }
        }
        stage('kill controller') {
          steps {
            build(job: 'canada-stop-controller', propagate: true, wait: true)
          }
        }
        stage('wait2') {
          steps {
            sleep(time: 8, unit: 'MINUTES')
          }
        }
      }
    }
    stage('Test') {
      parallel {
        stage('Test2') {
          steps {
            build(job: 'force-peering-switchover', propagate: true, wait: true)
          }
        }
        stage('force-peering-switchover') {
          steps {
            build(job: 'force-peering-switchover', propagate: true, wait: true)
          }
        }
        stage('wait3') {
          steps {
            sleep(unit: 'MINUTES', time: 8)
          }
        }
      }
    }
    stage('Stop Controller2') {
      parallel {
        stage('Trigger Controller HA') {
          steps {
            addBadge(icon: 'Stop', text: 'Stop Controller 2nd time')
          }
        }
        stage('kill controller') {
          steps {
            build(job: 'canada-stop-controller', propagate: true, wait: true)
          }
        }
        stage('wait4') {
          steps {
            sleep(unit: 'MINUTES', time: 8)
          }
        }
      }
    }
    stage('Delete Stack') {
      parallel {
        stage('Delete Stack') {
          steps {
            echo 'Delete cloudformation stack'
          }
        }
        stage('transit-upgrade') {
          steps {
            build 'transit-upgrade'
          }
        }
        stage('destroy stack') {
          steps {
            build(job: 'canada-controllerHA-stack-destroy', propagate: true, wait: true)
          }
        }
      }
    }
    stage('Report') {
      parallel {
        stage('Report') {
          steps {
            slackSend(failOnError: true, message: 'ControllerHA Regression Results - 100% Passed', baseUrl: 'https://aviatrix.slack.com/services/hooks/jenkins-ci/', channel: '#sitdown', teamDomain: 'aviatrix', token: 'zjC6JXcuigU1Nq0j3AoLBdci')
          }
        }
        stage('send email') {
          steps {
            mail(subject: 'ControllerHA Regression Results - 100% Passed', to: 'edsel@aviatrix.com', body: 'create CFT stack, transit-upgrade, stop controller, wait, force-peering-switchover, stop controller, transit-upgrade, slack notification, send email')
          }
        }
      }
    }
  }
}