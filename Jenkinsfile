@Library('jenkins-assessment-shared-lib')_

pipeline {
    
    agent any
    
    options { 
        buildDiscarder(logRotator(daysToKeepStr: '5', numToKeepStr: '5'))
    }
    
    stages{
    		stage('Clone-Repo') {
    		    steps {
    		        script{
        		        try {
                		        sharedEmailNotification (
                                    [
                                        "email": "${NOTIFICATION_EMAIL}",
                                        "status":  "STARTED"
                                    ]
                                )
                    		    sharedCloneRepository (
                                    [
                                        "branch": "${GIT_CLONE_REPO_BRANCH}",
                                        "credentials":  "Github-Credential",
                                        "url": "${GIT_CLONE_REPO_URL}"
                                    ]
                                )
        		        } catch (e) {
                            echo 'Err: Error while cloning repository : ' + e.toString()
                            throw e
                        }
    		        }
    		    }
    		}
    		
    		stage('Build-Project') {
    		    steps {
    		        script{
        		        try {
            		        echo 'Building Project...'
                		    sh label: 'mvn', script: 'mvn clean install -Pjenkins -DBUILD_NUMBER=${BUILD_NUMBER}'
        		        } catch (e) {
                            echo 'Err: Error while building project : ' + e.toString()
                            throw e
                        }
    		        }
    		    }
    		}
    		
    		stage('Execute-Tests') {
                steps {
                    script{
        		        try {
                            echo 'Executing Tests...'
                            sh 'mvn test'
        		        } catch (e) {
                            echo 'Err: Error executing tests : ' + e.toString()
                            throw e
                        }
    		        }
                }
            }
            
            stage('Code-Coverage') {
                steps {
                    script{
        		        try {
                            echo 'Executing Code Coverage...'
                            jacoco()
                            step([$class: 'JUnitResultArchiver', testResults: 'target/surefire-reports/TEST-*.xml'])
                            publishHTML(
                                [allowMissing: false, 
                                alwaysLinkToLastBuild: true, 
                                keepAll: true, 
                                reportDir: 'target/', 
                                reportFiles: 'index.html', 
                                reportName: 'HTMLReport', 
                                reportTitles: 'Code Coverage'])
        		        } catch (e) {
                            echo 'Err: Error executing code coverage : ' + e.toString()
                            throw e
                        }
    		        }
                }
            }
            
            stage('Sonarqube-Scan') {
                environment {
                    scannerHome = tool 'SonarQubeScanner'
                }
                steps {
                    withSonarQubeEnv('sonarqube') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                    timeout(time: 10, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
            
            stage('Archive-Artifacts') {
    		    steps {
    		        script{
        		        try {
            		        echo 'Archiving Artifacts...'
                		    archiveArtifacts artifacts: 'target/*.jar'
        		        } catch (e) {
                            echo 'Err: Error archiving artifacts : ' + e.toString()
                            throw e
                        }
    		        }
    		    }
    		}
            
            stage("Publish-Artifacts") {
                steps {
                    script{
        		        try {
                           sharedPublishArtifacts (
                                [
                                    "nexusUrl": "${NEXUS_URL}",
                                    "groupId":  "${ARTIFACT_GROUP_ID}",
                                    "repository": "${ARTIFACT_REPOSITORY_NAME}",
                                    "credentials": "Nexus-Credential",
                                    
                                ]
                            )
        		        } catch (e) {
                            echo 'Err: Error publishing artifacts : ' + e.toString()
                            throw e
                        }
    		        }
                }
            }
    }
	    
    post {
        always {
            echo 'Pipeline executed.'
        }
        success {
            echo 'Job succeeeded!'
            sharedEmailNotification (
                    [
                        "email": "${NOTIFICATION_EMAIL}",
                        "status":  "SUCCESS"
                    ]
                )
        }
        unstable {
            echo 'Project is unstable.'
            sharedEmailNotification (
                    [
                        "email": "${NOTIFICATION_EMAIL}",
                        "status":  "UNSTABLE"
                    ]
                )
        }
        failure {
            echo 'Pipeline failed.'
            sharedEmailNotification (
                    [
                        "email": "${NOTIFICATION_EMAIL}",
                        "status":  "FAILED"
                    ]
                )
        }
        changed {
            echo 'Pipeline status changed.'
        }
    }
    
}
