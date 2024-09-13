pipeline {
  agent any

  tools {
    nodejs 'nodejs-22-6-0'
  }

  stages {
    stage('Install Dependencies') {
      steps {
        sh 'npm install --no-audit'
      }
    }

    stage('Dependency Scanning') {
      parallel {
        stage('NPM Dependency Audit') {
          steps {
            sh 'npm audit --audit-level=critical'
          }
        }

        stage('OWASP Dependency Check') {
          steps {
            dependencyCheck(additionalArguments: ''' 
              --scan \'./\' 
              --out \'./\'  
              --format \'ALL\' 
              --disableYarnAudit
              --prettyPrint ''', odcInstallation: 'OWASP-DepCheck-10')
              dependencyCheckPublisher(failedTotalCritical: 1,
                pattern: 'dependency-check-report.xml',
                stopBuild: true)
            }
          }
        }
      }
    }
    post {
      always {
        publishHTML(allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: './', reportFiles: 'dependency-check-jenkins.html', reportName: 'Dependency Check HTML Report', useWrapperFileDirectly: true)
      }
    }
  }