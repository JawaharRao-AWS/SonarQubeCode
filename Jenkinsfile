def registry = 'https://jawahar.jfrog.io'
pipeline{
    agent any
    environment{
        PATH="/opt/maven/bin:$PATH"
    }
    stages{
        stage("Build"){
            steps{
                sh "mvn clean deploy -Dmaven.test.skip=true"
            }
        }
        stage("Test"){
            steps{
                sh "mvn surefire-report:report"
            }
        }
        stage("Quality Analysis"){
            environment{
                ScannerHome= tool "Sonar-Scanner"
            }
            steps{
                withSonarQubeEnv("SonarQube-Server"){
                    sh "${ScannerHome}/bin/sonar-scanner"
                }
            }
        }
	stage("Jar Publish"){
	   steps{
	       script{
		   def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "artifactory-credentials"
		   def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
		   def uploadSpec = """{
			"files": [
			  {
		            "pattern": "jarstaging/(*)",
			    "target": "jawahar-libs-release-local/{1}",
			    "flat": "false",
			    "props": "${properties}",
			    "exclusions": ["*.sha1", "*.md5"] 	
			  }
		        ]	
		   }"""
		  def buildInfo = server.upload(uploadSpec)
		  buildInfo.env.collect()
		  server.publishBuildInfo(buildInfo)
              }
	  }
	}									  		 		
	      	
    }
}
