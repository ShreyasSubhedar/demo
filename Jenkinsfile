pipeline {
    agent any
  environment {
    RULE_APP_NAME = "${RULE_APP_NAME}"
    BRANCH_NAME = "${BRANCH_NAME}"
    DEPLOYMENT_NAME = "${DEPLOYMENT_NAME}"
  }
    stages {
        stage('/decisionservices ID using role app name ') {
            steps {
                script {
                    // curl 1
                    def endpoint = 'https://odm-decisioncenter-dev.apps.openshift.dev.com/decisionservices'
                    def decisoinServiceID = null
                    def branchID = null
                    def deploymentID = null
                    def response1  = sh(script: "curl -k --location --request GET '${endpoint}'", returnStdout: true)
                    def decisionServiceResponse = readJSON text: response1
                    print(decisionServiceResponse)
                    for (element in decisionServiceResponse?.elements) {
                        if (element.name == env.RULE_APP_NAME) {
                            decisoinServiceID = element?.id
                        }
                    }
                    if(decisoinServiceID == null) {
                        error ("The rule app name ${env.RULE_APP_NAME} does not found.")
                    } 
                    
                    
                    // curl 2
                    endpoint = 'https://odm-decisioncenter-dev.apps.openshift.dev.com/decisionservices/' + \
                    "${decisoinServiceID}/branches"
                    def response2  = sh(script: "curl -k --location --request GET '${endpoint}'", returnStdout: true)
                    def branchesResponse = readJSON text: response2
                    for (element in branchesResponse.elements) {
                        if (element.name == env.BRANCH_NAME) {
                            branchID = element.id
                        }
                    }
                    if (branchID == null) {
                        error("The branch name ${env.BRANCH_NAME} does not found.")
                    }
                    
                    // curl 3
                    endpoint = 'https://odm-decisioncenter-dev.apps.openshift.dev.com/decisionservices/' + \
                    "${branchID}/deployments?baselineid=123684627588"
                    def response3  = sh(script: "curl -k --location --request GET '${endpoint}'", returnStdout: true)
                    def deploymentResponse = readJSON text: response3
                    for (element in deploymentResponse.elements) {
                        if (element.name == env.DEPLOYMENT_NAME) {
                            deploymentID = element.id
                        }
                    }
                    if (deploymentID == null) {
                        error("The deployment name ${DEPLOYMENT_NAME} does not found.")
                    }
                    echo "Ans : ${RULE_APP_NAME} ${deploymentID}"
                }
            }
        }
    }
}
build job: 'DEVOPS-jar-creation', parameters: [string(name: 'RULE_APP_NAME', value: "${RULE_APP_NAME}"), string(name: 'ID', value: "${DEPLOYMENT_ID}")]
