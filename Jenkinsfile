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
            sh(script: 'docker compose exec -T azure-vote-front pytest /tests/test_sample.py')
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
      stage('Docker Push') {
         steps {
            echo "RUNNING IN $WORKSPACE"
            dir("$WORKSPACE/azure-vote"){
               script {
                  docker.withRegistry('', 'Dockerhub') {
                     def image = docker.build("fahmidovi/jenkins:v1")
                     image.push()
                  }
               }
            }
         }
      }
   }
   post {
         always {
            sh(script: 'docker compose down')
         }
      }
}