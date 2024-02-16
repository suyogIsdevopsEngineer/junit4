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

        stage('Publish Cobertura Report') {
            steps {
                // Publish Cobertura report in Jenkins
                cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', conditionalCoverageTargets: '', enableNewApi: true, failUnhealthy: false, failUnstable: false, healthyTarget: [methodCoverage: 70, classCoverage: 80, conditionalCoverage: 80], onlyStable: false, sourceEncoding: 'UTF-8', unstableTarget: [methodCoverage: 0, classCoverage: 0, conditionalCoverage: 0]
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
    }