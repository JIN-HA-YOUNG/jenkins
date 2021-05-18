pipeline {
  agent any
  stages {
    stage('Prepare') {
      agent any
      post {
        success {
          echo 'Successfully Cloned Repository'
        }

        always {
          echo 'i tried...'
        }

        cleanup {
          echo 'after all other post condition'
        }

      }
      steps {
        echo 'Clonning Repository'
        git(url: 'https://github.com/JIN-HA-YOUNG/jenkins.git', branch: 'master', credentialsId: 'JIN-HA-YOUNG')
      }
    }

    stage('Deploy Frontend') {
      post {
        success {
          echo 'Successfully Cloned Repository'
        }

        failure {
          echo 'I failed :('
        }

      }
      steps {
        echo 'Deploying Frontend'
        dir(path: './website') {
          sh '''
                aws s3 sync ./ s3://hayoungjin
                '''
        }

      }
    }

    stage('Lint Backend') {
      agent {
        docker {
          image 'node:latest'
        }

      }
      steps {
        dir(path: './server') {
          sh '''
                  npm install&&
                  npm run lint
                  '''
        }

      }
    }

    stage('Test Backend') {
      agent {
        docker {
          image 'node:latest'
        }

      }
      steps {
        echo 'Test Backend'
        dir(path: './server') {
          sh '''
                npm install
                npm run test
                '''
        }

      }
    }

    stage('Bulid Backend') {
      agent any
      post {
        failure {
          error 'This pipeline stops here...'
        }

      }
      steps {
        echo 'Build Backend'
        dir(path: './server') {
          sh '''
                docker build . -t server --build-arg 
                '''
        }

      }
    }

    stage('Deploy Backend') {
      agent any
      post {
        success {
          echo 'Build Success'
        }

      }
      steps {
        echo 'Build Backend'
        dir(path: './server') {
          sh '''
                docker run -p 80:80 -d server
                '''
        }

      }
    }

  }
  environment {
    AWS_ACCESS_KEY_ID = credentials('awsAccessKeyId')
    AWS_SECRET_ACCESS_KEY = credentials('awsSecretAccessKey')
    AWS_DEFAULT_REGION = 'us-west-1'
    HOME = '.'
  }
  triggers {
    pollSCM('*/3 * * * *')
  }
}