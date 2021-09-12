
pipeline {
  agent any
    
  options { 
    timestamps () 
    buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '10', numToKeepStr: '5')	
// numToKeepStr - Max # of builds to keep
// daysToKeepStr - Days to keep builds
// artifactDaysToKeepStr - Days to keep artifacts
// artifactNumToKeepStr - Max # of builds to keep with artifacts	  
}	
  environment {
    SONAR_HOME = "${tool name: 'sonar-scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'}"
    DOCKER_TAG = DockerTag()	  
  }  
  stages {
   
    stage('Execute_Maven') {
	  steps {
	    script {
		  rtMaven.run pom: 'pom.xml', goals: 'clean install', buildInfo: buildInfo
        }			                      
      }
    }	
   
    stage('SonarQube_Analysis') {
      steps {
	    script {
          scannerHome = tool 'sonar-scanner'
        }
        withSonarQubeEnv('sonar') {
      	  sh """${scannerHome}/bin/sonar-scanner"""
        }
      }	
    }	
	stage('Quality_Gate') {
	  steps {
	    timeout(time: 3, unit: 'MINUTES') {
		  waitForQualityGate abortPipeline: true
        }
      }
    }

      
      post {
    always {
	mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "Success: Project name -> ${env.JOB_NAME}", to: "prithdileep@gmail.com";
    }
    failure {
	sh 'echo "This will run only if failed"'
      mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "ERROR: Project name -> ${env.JOB_NAME}", to: "prithdileep@gmail.com";
    }
  }
}
  
