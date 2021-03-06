pipeline {
     agent any
     triggers {
          pollSCM('* * * * *')
     }
     
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
                    sh "docker build -t georgesngandeu/calculator ."
               }
          }

          stage("Docker login") {
               steps {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: '6a8d4745-dfe9-4840-8e17-04b652018964',
                               usernameVariable: 'username', passwordVariable: 'password']]) {
                         sh "docker login --username $username --password $password"
                    }
               }
          }

          stage("Docker push") {
               steps {
                    sh "docker push georgesngandeu/calculator"
               }
          }
          
          stage("Deploy to staging") {
               steps {
                    sh "docker run -d --rm -p 8765:8080 --name calculator georgesngandeu/calculator"
               }
          }

          stage("Acceptance test") {
               steps {
                    echo 'Some acceptance test here'
               }
          }
     }
     post {
          always {
               sh "docker stop calculator"
          }
     }
}