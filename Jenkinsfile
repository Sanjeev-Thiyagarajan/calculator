pipeline {
     agent any
     stages {
          stage("Compile") {
               steps {
                    sh "./gradlew compileJava"
               }
          }
          stage("Unit test") {
               steps {
                    sh "./gradlew test"
               }
          }
          stage("Code coverage") {
               steps {
                    sh "./gradlew jacocoTestReport"
                    publishHTML (target: [
                         reportDir: 'build/reports/jacoco/test/html',
                         reportFiles: 'index.html',
                         reportName: "JaCoCo Report"
                    ])
                    sh "./gradlew jacocoTestCoverageVerification"
               }
          }
          stage("Static code analysis") {
               steps {
                    sh "./gradlew checkstyleMain"
               }
          }
          stage("Package") {
               steps {
                    sh "./gradlew build"
               }
          }

          stage("Docker build") {
               steps {
                    sh "docker build -t sloppynetworks/java ."
               }
          }
          stage("Docker Push") {
               steps {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'password', usernameVariable: 'username')]) {
                           
                           sh 'echo $password | docker login --username=$username --password-stdin'
                    }
                    
                    sh "docker push sloppynetworks/java"
               }
          }
          // stage("Deploy to staging") {
          //      steps {
          //           sh "docker run -d --rm -p 8765:8080 --name calculator sloppynetworks/java"
          //      }
          // }
          stage("Deploy to staging") {
               steps {
                    sh "docker-compose up -d"
               }
          }
          stage("Acceptance test") {
               steps {
                    sleep 60
                    sh "./acceptance_test.sh"
               }
          }
     }
     // post {
     //      always {
     //           sh "docker stop calculator"
     //      }
     // }
     post {
          always {
               sh "docker-compose down"
          }
     }
}
