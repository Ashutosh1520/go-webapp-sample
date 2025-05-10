pipeline {
  agent any
  tools { 
    go 'go-1.24.2'
  }
  environment {
    GO111MODULE='on'
    CGO_ENABLED='1'
    GOOS='limux'
    GOARCH='amd64'
  }
  triggers{
    githubPush()
  }
  stages {
    stage ('Checkout') {
      step {
        sh 'git https://github.com/Ashutosh1520/go-webapp-sample.git'
      }
    }
    stage('Test') {
      steps {
        sh 'go test ./...'
      }
    }
    stage ('Build') {
      steps {
        sh 'go build -o go-webapp-sample'
      }
    }
    stage ('Archive Artifacts') {
      steps {
        archiveArtifacts artifacts: 'go-webapp-sample', fingerprint: true
      }
    }
    stage ('Deploy to Dev') {
      steps {
        echo 'Deploying to Dev...'
        sh '''
            pkill -f go-webapp-sample || true
            nohup ./go-webapp-sample > dev.log 2>&1 &
           '''
      }
    }
    stage ('Approval for Staging') {
      steps {
        input message: 'Approve Deployment to Staging', ok: 'Deploy'
        }
    }
    stage ('Deploy to Staging') {
      steps {
        echo 'Deploying to Staging...'
        sh '''
            pkill -f go-webapp-sample || true
            nohup ./go-webapp-sample > staging.log 2>&1 &
           '''
      }
    }
    stage ('Approval for Production') {
      steps {
        input message: 'Approve final Production Deployment', ok: 'Go Live'
      }
    }
    stage ('Deploy To Production') {
      steps {
        echo 'Deploying to Production...'
        sh '''
            pkill -f go-webapp-sample || true
            nohup ./go-webapp-sample > prod.log 2>&1 &
           '''
      }
    }
  post {
    failure {
      mail to: 'ashutoshgiri1506@gmail.com',
        subject: "Pipeline Failed: ${env.JOB_NAME} [${env.BUILD_NUMBER}]"
        body: "Check Jenkins for details: ${env.BUILD_URL}"
    }
  }       
  }
}
