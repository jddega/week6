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
