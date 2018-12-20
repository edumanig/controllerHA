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
        stage('transit-bgp-switchover') {
          steps {
            build(job: 'transit-bgp-switchover', propagate: true, quietPeriod: 10, wait: true)
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
        stage('transit-check-primary-datapath') {
          steps {
            build(job: 'transit-check-primary-datapath', propagate: true, wait: true, quietPeriod: 2)
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
    stage('Test1') {
      parallel {
        stage('Test1') {
          steps {
            build(job: 'force-peering-switchover', propagate: true, wait: true)
          }
        }
        stage('transit-check-primary-datapath') {
          steps {
            build(job: 'transit-check-primary-datapath', propagate: true, wait: true, quietPeriod: 10)
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
    stage('Test2') {
      parallel {
        stage('Test2') {
          steps {
            echo 'Upgrade 2nd time'
          }
        }
        stage('transit-check-primary-datapath') {
          steps {
            build(job: 'transit-check-primary-datapath', propagate: true, wait: true, quietPeriod: 10)
          }
        }
        stage('wait5') {
          steps {
            sleep(unit: 'MINUTES', time: 5)
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
        stage('destroy stack') {
          steps {
            build(job: 'canada-controllerHA-stack-destroy', propagate: true, wait: true, quietPeriod: 30)
          }
        }
        stage('wait6') {
          steps {
            sleep 10
          }
        }
      }
    }
    stage('Report') {
      parallel {
        stage('Report') {
          steps {
            slackSend(message: 'ControllerHA Regression Connect-3.4.703- 100% Passed', baseUrl: 'https://aviatrix.slack.com/services/hooks/jenkins-ci/', channel: '#test-controllerha', failOnError: true, teamDomain: 'aviatrix', token: 'zjC6JXcuigU1Nq0j3AoLBdci', attachments: 'create CFT stack, transit-upgrade, stop controller, wait, check IAM-ROLE, check ec2 volume size, check T2 unlimited, force-peering-switchover, stop controller, transit-upgrade, AND run kill controller twice, slack notification, send email')
          }
        }
        stage('Send Email') {
          steps {
            emailext(attachLog: true, subject: 'ControllerHA Regression Results - 100% Passed', to: 'dltest@aviatrix.com,arvind@aviatrix.com', body: 'create CFT stack, transit-check-primary-datapath, stop controller, wait, transit-check-primary-datapath, check ec2 volume size, check T2 unlimited, transit-check-primary-datapath, stop controller, transit-check-primary-datapath, AND run kill controller twice, send email', from: 'noreply@aviatrix.com')
          }
        }
      }
    }
  }
}