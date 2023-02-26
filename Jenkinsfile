<<<<<<< HEAD
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
                    sh "docker build -t leszko/calculator:${BUILD_TIMESTAMP} ."
               }
          }

          stage("Docker login") {
               steps {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'docker-hub-credentials',
                               usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                         sh "docker login --username $USERNAME --password $PASSWORD"
                    }
               }
          }

          stage("Docker push") {
               steps {
                    sh "docker push leszko/calculator:${BUILD_TIMESTAMP}"
               }
          }

          stage("Update version") {
               steps {
                    sh "sed  -i 's/{{VERSION}}/${BUILD_TIMESTAMP}/g' calculator.yaml"
               }
          }
          
          stage("Deploy to staging") {
               steps {
                    sh "kubectl config use-context staging"
                    sh "kubectl apply -f hazelcast.yaml"
                    sh "kubectl apply -f calculator.yaml"
               }
          }

          stage("Acceptance test") {
               steps {
                    sleep 60
                    sh "chmod +x acceptance-test.sh && ./acceptance-test.sh"
               }
          }

          stage("Release") {
               steps {
                    sh "kubectl config use-context production"
                    sh "kubectl apply -f hazelcast.yaml"
                    sh "kubectl apply -f calculator.yaml"
               }
          }
          stage("Smoke test") {
              steps {
                  sleep 60
                  sh "chmod +x smoke-test.sh && ./smoke-test.sh"
              }
          }
     }
}
=======
podTemplate(containers: [
  containerTemplate(
    name: 'gradle',
    image: 'gradle:6.3-jdk14',
    command: 'sleep',
    args: '30d'
    ),
  ]) {
    node(POD_LABEL) {
      stage('Run pipeline against a gradle project - test MAIN') {
	container('gradle') {
          stage('Build a gradle project') {
            echo "I am the ${env.BRANCH_NAME} branch"
	    sh '''
             cd chapter08/sample1
             chmod +x gradlew 
	     ./gradlew test'''
          }
          stage('feature branch') {
	    sh 'printenv'
            echo "My CC branch is: ${env.CHANGE_BRANCH}"
            if (env.BRANCH_NAME == "PR") {
              echo "I am the ${env.BRANCH_NAME} branch"
	      try {
              sh '''
              pwd
              cd Chapter08/sample1
                ./gradlew jacocoTestCoverageVerification
                ./gradlew jacocoTestReport '''
            } catch (Exception E) {
              echo 'Failure detected'
            }
	      // from the HTML publisher plugin
              // https://www.jenkins.io/doc/pipeline/steps/htmlpublisher/
              publishHTML(target: [
                reportDir: 'Chapter08/sample1/build/reports/tests/test',
                reportFiles: 'index.html',
                reportName: "JaCoCo Report"
              ])
	  stage('Code coverage') {
	    sh 'printenv'
            echo "My CC branch is: ${env.CHANGE_BRANCH}"
            if (env.BRANCH_NAME == "main") {
              echo "I am the ${env.BRANCH_NAME} branch"
	      try{
	      sh '''
              pwd
              cd Chapter08/sample1'''
	      } catch (Exception E) {
		echo 'Failure detected'
	      }
	    }
          }
        }
      }   
    }
  }
 }
}
>>>>>>> 398cf8f9b6a914f5a437516c1b7bed55b7144b37
