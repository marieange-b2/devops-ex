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
			post {
				success {
					echo 'Generated Chekstyle analysis results.'
				}
			}
		}

		stage('Checkstyle Analysis'){
			steps {
				sh 'mvn checkstyle:checkstyle'
			}
		}
	}
}

