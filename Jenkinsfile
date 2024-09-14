properties(
[pipelineTriggers()]
)


def FAILED_STAGE

pipeline {
    // agent { label "master" }
    agent any
    stages {
        stage('CI STAGE') {
            when {
                anyOf {
                    branch 'master'
                    branch 'develop'
                    branch 'staging'
                    branch 'development'
                }
            }
            steps {

                script {
                    echo "${env.BRANCH_NAME}"
                }
                
                sh label: "${env.BRANCH_NAME}", script:
                """
                lbebuild ${env.BRANCH_NAME}
                lberelease
                """
            }
        }


        stage('CD STAGE') {
            when {
                anyOf {
                    branch 'master'
                    branch 'develop'
                    branch 'staging'
                    branch 'development'
                }
            }
            steps {

                script {
                    echo "${env.BRANCH_NAME}"
                }
                
                sh label: "${env.BRANCH_NAME}", script:
                """
                lhelmsetinit ${env.BRANCH_NAME}
                lhelmupgrade ${env.BRANCH_NAME}
                """
            }

        }
    }


//POST============================================================
    post {
        failure {
            // echo "Failed stage name: ${FAILED_STAGE}"
            // notif failure
            sh label: 'notif failure', script:
            """
                notif fail "STEP ==> ${FAILED_STAGE} #${env.JOB_NAME} , Job No #${env.BUILD_NUMBER} is FAILURE ==> ${env.BUILD_URL}"
            """
        //
        }
        success {
            // notif success
            sh label: 'notif success', script:
            """
                notif success "${env.JOB_NAME} , Job No #${env.BUILD_NUMBER} is SUCCESS"
            """
        }

    }
}
