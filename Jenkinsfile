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
                    def branchName = ${env.BRANCH_NAME}
                    def tagName = ${env.TAG_NAME}

                    if (tagName == null || tagName.trim() == ''){
                        sh 'lbebuild ${env.BRANCH_NAME}'
                        sh 'lberelease'
                    }else if(branchName == 'staging') {
                        sh 'lbedockerpull'
                        sh 'lbedockertag ${tagName}'
                        sh 'lbedockertagpush ${tagName}'
                    }else {
                        echo "nama branch ${branchName}"
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
                    echo "${env.BRANCH_NAME}"
                }
                
                sh label: "${env.BRANCH_NAME}", script:
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
                    echo "${env.BRANCH_NAME}"
                    def branchName = ${env.BRANCH_NAME}
                    def tagName = ${env.TAG_NAME}

                    if (tagName == null || tagName.trim() == ''){
                        sh 'lclone gitops ${env.BRANCH_NAME}'
                        sh 'lbesetimage ${env.BRANCH_NAME}'
                        sh 'cd gitops'
                        sh 'git commit -am "${env.GIT_COMMIT}"'
                        sh 'git push origin ${env.BRANCH_NAME}'
                    }else if(branchName == 'staging'){
                        echo "Baru sampai push dulu"
                    }else {
                        echo "nama branch ${branchName}"
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
