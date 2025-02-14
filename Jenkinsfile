@Library('my-shared-librabry') _

pipeline {
    agent any

    parameters{
        string(name: 'ImageName', description: "Name of docker build", defaultValue:  'javapp')
        string(name: 'ImageTag', description: "Tag of docker build", defaultValue:  'v1')
        string(name: 'hubUser', description: "Name of docker build", defaultValue:  'imsuyog')
    }
	
    options {
		skipDefaultCheckout(true)
   }

    stages{
        
        stage('Git Checkout'){ 
        steps {
            script{
				git branch: "main", url: 'https://github.com/suyogIsdevopsEngineer/junit4.git'
			}             
            }
        }

       stage('Unit test Maven'){
        steps {
			script{
				mvnTest()
			}  
        }
        }

        
        stage('Integration Test maven'){
            steps{
				script{
					mvnIntegration()
				}
            }
        }
   
            stage('Static code Analysis: Sonarqube'){
            steps{
				script{
                    
					def SonaraqubeCredentialsId = 'sonar'
                    sonarStaticAnalysis(SonaraqubeCredentialsId)
				}
            }
        } 

        stage('Maven Build : maven'){
            steps{
				script{
                    mvnBuild()
				}
            }
        }

        stage('Jacoco code coverage'){
            steps{
                script{
                    mvn jacoco:report 
                    archiveArtifacts artifacts: '**/target/site/jacoco/index.html', fingerprint: true
                }
            }
        }

        stage('docker Image build'){
            steps{
				script{
                    
                    dockerBuild("${params.ImageName}","${params.hubUser}","${params.ImageTag}")
				}
            }
        }

        stage('docker Image Scan'){
            steps{
				script{
                    
                    dockerImageScan("${params.ImageName}","${params.hubUser}","${params.ImageTag}")
				}
            }
        }

        stage('docker Image push'){
            steps{
				script{
                    
                    dockerPush("${params.ImageName}","${params.hubUser}","${params.ImageTag}")
				}
            }
        }
    }
    post {
        always {
            // Publish Allure report
            allure([
                includeProperties: false,
                jdk: '',
                properties: [],
                reportBuildPolicy: 'ALWAYS',
                results: [[path: 'target/allure-results']]
            ])
        }
    }
    }