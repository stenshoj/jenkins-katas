 pipeline {
  agent any
  stages {
    stage('clone down') {
      steps {
        stash(name: 'code', excludes: '.git/')
      }
    }

    stage('Parallel execution') {
      parallel {
        stage('Say Hello') {
            options {
  skipDefaultCheckout true
}
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('build app') {
            options {
  skipDefaultCheckout true
}
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
              
            unstash 'code'
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            deleteDir()
            sh 'ls'
          }
        }

        stage('build app') {
            options {
  skipDefaultCheckout true
}
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
              
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
          post {
        always {

            deleteDir() /* clean up our workspace */
        }
        }

      }
    }

  }
}