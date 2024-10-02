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

    environment {
        BRANCH_NAME = ${env.BRANCH_NAME}
        TAG_NAME = ${env.TAG_NAME}
    }
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
                    echo "${BRANCH_NAME}"


                    if (TAG_NAME == null || TAG_NAME.trim() == ''){
                        sh "lbebuild ${BRANCH_NAME}"
                        sh "lberelease"
                    }else if(BRANCH_NAME == 'staging') {
                        sh "lbedockerpull"
                        sh "lbedockertag ${TAG_NAME}"
                        sh "lbedockertagpush ${TAG_NAME}"
                    }else {
                        echo "nama branch ${BRANCH_NAME}"
                    }
                }
            }
        }
        stage('DELETE IMAGE') {
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
                    echo "${BRANCH_NAME}"
                }
                
                sh label: "${BRANCH_NAME}", script:
                """
                docker system prune -af
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
                    echo "${BRANCH_NAME}"
                    def BRANCH_NAME = ${BRANCH_NAME}
                    def TAG_NAME = ${TAG_NAME}

                    if (TAG_NAME == null || TAG_NAME.trim() == ''){
                        sh "lclone gitops ${BRANCH_NAME}"
                        sh "lbesetimage ${BRANCH_NAME}"
                        sh "cd gitops"
                        sh 'git commit -am "${GIT_COMMIT}"'
                        sh "git push origin ${BRANCH_NAME}"
                    }else if(BRANCH_NAME == 'staging'){
                        echo "Baru sampai push dulu"
                    }else {
                        echo "nama branch ${BRANCH_NAME}"
                    }
                }
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
