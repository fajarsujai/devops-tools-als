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
                ldbuild ${env.BRANCH_NAME}
                ldrease
                """
            }
        }


        // stage('CD STAGE') {
        //     when {
        //         anyOf {
        //             branch 'master'
        //             branch 'develop'
        //             branch 'staging'
        //             branch 'development'
        //         }
        //     }
        //     steps {

        //         script {
        //             echo "${env.BRANCH_NAME}"
        //         }
                
        //         sh label: "${env.BRANCH_NAME}", script:
        //         """
        //         ldhelmupgrade ${env.BRANCH_NAME}-${PROJECT}
        //         """
        //     }

        // }
    }
}
