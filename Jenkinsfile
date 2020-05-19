 pipeline {
  agent any
  environment { 
        docker_username = 'stenshoj'
    }
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

        stage('test app') {
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
    stage('push docker app') {
      environment {
      DOCKERCREDS = credentials('docker_login') //use the credentials just created in this stage
}
steps {
      unstash 'code' //unstash the repository code
      sh 'ci/build-docker.sh'
      sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin' //login to docker hub with the credentials above
      sh 'ci/push-docker.sh'
}
    }

  }
}
 }