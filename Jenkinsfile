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
    }
}
