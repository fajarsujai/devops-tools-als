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
        BRANCH_NAME = "${env.BRANCH_NAME}"
        TAG_NAME = "${env.TAG_NAME}"
    }

    stages {
        stage('CI Stage (staging)') {
            when {
                branch 'staging'
            }
            steps {
                script {
                    echo "${BRANCH_NAME}"
                    sh "lbedockerpull"
                    sh "lbedockertag ${TAG_NAME}"
                    sh "lbedockertagpush ${TAG_NAME}"
                }
            }
        }

        stage('CI STAGE (STA)') {
            when {
                buildingTag()
            }
            steps {
                script {
                    echo "${TAG_NAME}"
                    sh "lbebuild ${BRANCH_NAME}"
                    sh "lberelease"
                }
            }
        }

        stage('Prune Docker') {
            steps {
                script {
                    sh "docker system prune -af"
                }
            }

        }

        stage('CD Stag (staging)') {
            when {
                branch 'staging'
            }
            steps {
                script {
                    echo "${BRANCH_NAME}"                    
                    sh "lclone gitops ${BRANCH_NAME}"
                    sh "lbesetimage ${BRANCH_NAME}"
                    sh "cd gitops"
                    sh 'git commit -am "${GIT_COMMIT}"'
                    sh "git push origin ${BRANCH_NAME}"
                }
            }

        }

        stage('CD Stag (tag)') {
            when {
                buildingTag()
            }
            steps {
                script {
                    echo "${TAG_NAME}"
                    echo "Baru sampai push dulu"
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
