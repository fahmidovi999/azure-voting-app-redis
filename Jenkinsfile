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
      stage('Run Clair') {
         steps {
            sh(script: 'docker create network clair-net 2>/dev/null || true')
            sh(script: 'docker run -d --name postgres --network clair-net -e POSTGRES_PASSWORD=clair -e POSTGRES_USER=clair -e POSTGRES_DB=clair postgres:14')
            sh(script: 'docker run -d --name clair --network clair-net -p 6060:6060 -p 6061:6061 quay.io/projectquay/clair:latest')
         }
      }
      stage ('Run Clair Scan') {
         steps {
            sh(script: 'docker pull fahmidovi/jenkins:v1')
            sh(script: 'clairctl analyze fahmidovi/jenkins:v1 --clair=http://localhost:6060')
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
   }
   post {
         always {
            sh(script: 'docker compose down')
         }
      }
}