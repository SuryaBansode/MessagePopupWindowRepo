pipeline {
	    agent any
	
	        // Environment Variables for RPA - NWL Ver4
	        environment {
	        MAJOR = '1'
	        MINOR = '0'
	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
	        UIPATH_ORCH_LOGICAL_NAME = "atossgathen"
	        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
	        UIPATH_ORCH_FOLDER_NAME = "NFLFolder"
	        UIPATH_ORCH_USER_NAME = "WW930\\A710005"
	    }
		stages {
	

	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
					checkout scm
	            }
	        }
			
			// Build Stages
	        stage('Build') {
	            steps {
	                echo "Building..with ${WORKSPACE}"
					UiPathPack (
	                      outputPath: "Output\\${env.BUILD_NUMBER}",
	                      projectJsonPath: "project.json",
	                      version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
	                      useOrchestrator: false,
						  traceLevel: 'None'
					)
	            }
	        }
	         // Test Stages
	        stage('Test') {
	            steps {
	                echo "Testing..the workflow..."
	            }
	        }
	

	         // Deploy Stages
	        stage('Deploy to UAT') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to UAT "
					UiPathDeploy (
						packagePath: "Output\\${env.BUILD_NUMBER}",
						orchestratorAddress: "${UIPATH_ORCH_URL}",
						orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
						folderName: "${UIPATH_ORCH_FOLDER_NAME}",
						environments: 'DEV',
						//credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
						credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'), 
						traceLevel: 'None',
						entryPointPaths: 'Main.xaml',
						createProcess: true
					)
	                
	            }
	        }

		// Run Test
		stage ('RunTest') {
		    steps {
			echo "Executing job........ "
        			UiPathRunJob(
					jobType: Dynamically(jobsCount: 1, machine: 'DESKTOP-MROI029', user: "${UIPATH_ORCH_USER_NAME}"), 
          				credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'), 
          				failWhenJobFails: true,
          				folderName: "${UIPATH_ORCH_FOLDER_NAME}",
          				orchestratorAddress: "${UIPATH_ORCH_URL}",
          				orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
          				parametersFilePath: '',
          				priority: 'Low',
          				processName: 'MessagePopupWindow',
          				resultFilePath: "Output\\${env.BUILD_NUMBER}",
          				strategy: Dynamically(jobsCount: 1, machine: 'DESKTOP-MROI029', user: "${UIPATH_ORCH_USER_NAME}"), timeout: 3600, waitForJobCompletion: true, traceLevel: 'None'
        			)
			}
		}
	
	         // Deploy to Production Step
	        stage('Deploy to Production') {
	            steps {
	                echo "Deploy to Production"
	                }
	            }
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	

	    // 
	    post {
	        success {
	            echo "Deployment has been completed!"
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        always {
	            /* Clean workspace if success */
	            cleanWs()
	        }
	    }	
	}
