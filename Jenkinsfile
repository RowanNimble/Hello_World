pipeline {
    agent any

    environment {
        MAJOR = '1'
        MINOR = '1'
        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
        UIPATH_ORCH_LOGICAL_NAME = "nimble_institute"
        UIPATH_ORCH_TENANT_NAME = "Nimble_Institute"
        UIPATH_ORCH_FOLDER_NAME = "CICD Pipeline Testing"
    }

    stages {

        stage('Preparing') {
            steps {
                echo "Jenkins Home ${env.JENKINS_HOME}"
                echo "Jenkins URL ${env.JENKINS_URL}"
                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
                echo "Jenkins JOB Name ${env.JOB_NAME}"
                echo "GitHub BranchName ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Build Process') {
            steps {
                echo "Building package with ${WORKSPACE}"
                UiPathPack (
                    outputPath: "Output\\${env.BUILD_NUMBER}",
                    projectJsonPath: "project.json",
                    version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                    useOrchestrator: false,
                    traceLevel: 'None'
                )
            }
        }

        stage('Deploy Process') {
            steps {
                echo 'Deploying process to orchestrator...'
                UiPathDeploy (
                    packagePath: "Output\\${env.BUILD_NUMBER}",
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
                    folderName: "${UIPATH_ORCH_FOLDER_NAME}",
                    environments: 'INT',
                    credentials: Token(
                        accountName: "${UIPATH_ORCH_LOGICAL_NAME}",
                        credentialsId: 'APIUserKey'
                    ),
                    traceLevel: 'None',
                    entryPointPaths: 'Main.xaml'
                )
            }
        }
    }

    options {
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    post {
        success {
            echo 'Deployment has been completed!'
        }
        failure {
            echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
        }
        always {
            cleanWs()
        }
    }
}
