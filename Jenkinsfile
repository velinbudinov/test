pipeline {
	agent any
	tools {
		jdk 'HotSpot 1.8'
	}
	stages {
		stage('Checkout') {
			steps {
				checkout scm
			}
		}
                stage('Build') {
                        steps {
                                sh 'rm build/libs/*'
                                sh 'gradle build -x test -x componentTest'
                        }
                }
                stage('Unit Test') {
                        steps {
                                sh 'gradle test || true'
                                // modify test results LMT as Jenkins JUnit plug will fail if tests do not execute (they may not since we use Gradle's smart build mechanism)
                                sh 'touch build/test-results/test/*.xml'
                                junit 'build/test-results/test/*.xml'
                        }
                }
                stage('Component Test') {
                        steps {
                                sh 'gradle componentTest'
                                // modify test results LMT as Jenkins JUnit plug will fail if tests do not execute (they may not since we use Gradle's smart build mechanism)
                                sh 'touch build/test-results/componentTest/*.xml'
                                junit 'build/test-results/componentTest/*.xml'
                        }
                }
                stage('Deploy to Nexus') {
                        environment {
                                version = sh(returnStdout: true, script: '${WORKSPACE}/tools/bash/extract_version.sh').trim()
                        }
                        steps {
                                nexusArtifactUploader artifacts: [[artifactId: 'velocity-billing', classifier: '', file: "build/libs/velocity-billing-${version}.jar", type: 'jar']], credentialsId: 'NEXUS_DEPLOYMENT_USER', groupId: 'com.methodia.velocity', nexusUrl: 'nexus.methodia.com', nexusVersion: 'nexus2', protocol: 'http', repository: 'methodia', version: "${version}"
                        }
                }
	}
}
