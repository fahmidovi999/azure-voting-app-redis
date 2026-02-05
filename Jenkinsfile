pipeline {
   agent any

   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
      }
      stage('Docker Build') {
         steps {
            sh(script: 'docker compose build')
         }
      }
      stage('Start App') {
         steps {
            sh(script: 'docker compose up -d')
         }
      }
      stage('Run Tests') {
         steps {
            sh(script: 'docker compose exec -T azure-vote-front pytest /app/tests/test_sample.py')
         }
         
         post {
            success {
               echo 'Tests Passed! :)'
            }
            failure {
               echo 'Tests Failed! :('
            }
         }
      }
      stage('Run Grype') {
         steps {
            grypeScan autoInstall: false, repName: 'grypeReport_${JOB_NAME}_${BUILD_NUMBER}.txt', scanDest: 'registry:fahmidovi/jenkins:v1'
         }
         post {
            always {
                  recordIssues(
                     tools: [grype()],
                     aggragatingResults: true,
                  )
               }
            }
         }
      }

      // stage('Docker Push') {
      //    steps {
      //       echo "RUNNING IN $WORKSPACE"
      //       dir("$WORKSPACE/azure-vote"){
      //          script {
      //             docker.withRegistry('', 'Dockerhub') {
      //                def image = docker.build("fahmidovi/jenkins:v1")
      //                image.push()
      //             }
      //          }
      //       }
      //    }
      // }
      post {
         always {
            sh(script: 'docker compose down')
         }
      }
   }
   
