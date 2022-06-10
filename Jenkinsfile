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
                    def endpoint = 'https://odm-decisioncenter-dev.apps.openshift.dev.com/decisionservices'
                    def response  = httpRequest  contentType: 'APPLICATION_JSON',
                                        url: endpoint,
                                        authentication: 'ODM-credential', 
                                        wrapAsMultipart: false
                    def decisionServiceResponse = readJSON text: response.content
                    for (element in decisionServiceResponse?.elements) {
                        if (element.name == env.RULE_APP_NAME) {
                            env.DECICION_SERVICE_ID = element.id
                        }
                    }
                    if(env.DECICION_SERVICE_ID == null) {
                        error ("The rule app name ${env.RULE_APP_NAME} does not found.")
                    } 
                }
            }
        }

        stage('/branches ID using branch name') {
            steps {
                script {
                    String endpoint = 'https://odm-decisioncenter-dev.apps.openshift.dev.com/decisionservices/' + \
                    "${DECICION_SERVICE_ID}/branches"
                    def response  = httpRequest  contentType: 'APPLICATION_JSON', 
                            url: endpoint,
                            authentication: 'ODM-credential',
                            wrapAsMultipart: false
                    def branchesResponse = readJSON text: response.content
                    for (element in branchesResponse.elements) {
                        if (element.name == env.BRANCH_NAME) {
                            env.BRANCH_ID = element.id
                        }
                    }
                    if (env.BRANCH_ID == null) {
                        error("The branch name ${BRANCH_NAME} does not found.")
                    }
                }
            }
        }
        
        stage('/deployments ID using deployment name') {
            steps {
                script {
                    String endpoint = 'https://odm-decisioncenter-dev.apps.openshift.dev.com/decisionservices/' + \
                    "${BRANCH_ID}/deployments?baselineid=123684627588"
                    print("${endpoint}")
                    def response  = httpRequest  contentType: 'APPLICATION_JSON',
                        url: 'https://testsad.free.beeceptor.com/deployments',
                        authentication: 'ODM-credential',
                        wrapAsMultipart: false
                    def deploymentResponse = readJSON text: response.content
                    for (element in deploymentResponse.elements) {
                        if (element.name == env.DEPLOYMENT_NAME) {
                            env.DEPLOYMENT_ID = element.id
                        }
                    }
                    if (env.DEPLOYMENT_ID == null) {
                        error("The deployment name ${DEPLOYMENT_NAME} does not found.")
                    }
                    echo "Ans : ${RULE_APP_NAME} ${DEPLOYMENT_ID}"
                    
                    // echo "Triggering Jar creation job"
                    // ?build job: 'DEVOPS-jar-creation', parameters: [string(name: 'RULE_APP_NAME', value: "${RULE_APP_NAME}"), string(name: 'ID', value: "${DEPLOYMENT_ID}")]
                }
            }
        }
    }
}

    def response  = sh(script: "curl --location --request GET 'https://testsad.free.beeceptor.com/deployments'", returnStdout: true)
