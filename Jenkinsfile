properties(
[pipelineTriggers()]
)


def FAILED_STAGE

pipeline {
    agent { 
        node {
            label "master" 
        }  
    }
    stages {
                stage('INFORMATION STAGE') {
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
                echo "Node Name: ${env.NODE_NAME}"
                echo "Node Label: ${env.NODE_LABEL}"
                echo "Workspace: ${env.WORKSPACE}"
                echo "Job URL: ${env.JOB_URL}"
                echo "Job URL: ${env.GIT_COMMIT}"
                """
            }
        }
        
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


        stage('CLEAN UP IMAGE') {
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
                docker system prune -af
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
