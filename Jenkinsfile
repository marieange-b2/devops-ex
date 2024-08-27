pipeline {
	agent any
	tools {
		maven "MAVEN3"
		jdk "OracleJDK8"
	}
	stages {
		stage('Build') {
			steps {
				sh 'mvn install -DskipTests'
			}
			post {
				success {
					echo 'archiving artifacts'
					archiveArtifacts artifacts: '**/*.war'
				}
			}
		}
		stage('Unit Tests') {
			steps {
				sh 'mvn test'
			}
			
		}

		stage('Checkstyle Analysis'){
			steps {
				sh 'mvn checkstyle:checkstyle'
			}
			post {
				success {
					echo 'Generated Chekstyle analysis results.'
				}
			}
		}


		stage('Code Analysis with SonarQube') {
          
		  environment {
             scannerHome = tool 'sonar4.7'
          }

          steps {
            withSonarQubeEnv('sonar-server') {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }

            timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate abortPipeline: true
            }
          }
        }

		stage ("Publish to Nexus") {
        	steps {
        		nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${env.NEXUS_URL}:8081", 
                    groupId: 'QA', 
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}", 
                    repository: 'vprofile-repo', 
                    credentialsId: 'nexuslogin', 
                    artifacts: [
                        [artifactId: 'vproapp',
                        classifier: '',
                        file: 'target/vprofile-v2.war',
                        type: 'war'],
                    ]
                );

        	}
        }

	}
}

