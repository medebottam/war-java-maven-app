pipeline {
    agent any
    tools {
        maven 'M3'
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M3 = ${M3}"
                '''
            }
        }

        stage ('Build') {
            steps {
                sh 'mvn clean install' 
            }
        }
        stage('SonarQube analysis') { 
			steps { 
				sh '/opt/sonar-scanner-3.2.0.1227-linux/bin/sonar-scanner -Dproject.settings=./sonar-project.properties' 
			}
		}
		stage('Artifactory upload'){
            steps {
                script{
                    // Obtain an Artifactory server instance, defined in Jenkins --> Manage:
                    //def server = Artifactory.server artifactory
		    def server = Artifactory.server('artifactory')
                    // Read the download and upload specs:
                    //def downloadSpec = readFile 'jenkins-examples/pipeline-examples/resources/props-download.json'
                    def uploadSpec = readFile 'props-upload.json'

                    // Download files from Artifactory:
                    //def buildInfo1 = server.download spec: downloadSpec
                    // Upload files to Artifactory:
                    def buildInfo2 = server.upload spec: uploadSpec

                    // Merge the local download and upload build-info instances:
                    //buildInfo1.append buildInfo2

                    // Publish the merged build-info to Artifactory
                    server.publishBuildInfo buildInfo2
                }
            }
        }
    }
}
