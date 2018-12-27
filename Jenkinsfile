pipeline {
    // triggers {
    //     parameterizedCron(env.BRANCH_NAME == 'master' && env.GIT_AUTHOR_EMAIL.length() > 0  ? '''
    //     # schedule every single minute
    //     * * * * *''' : '')
    // }
    agent any
    stages{
        stage('Build'){
            steps {
                echo "${env.BRANCH_NAME}"
                echo "${env.GIT_BRANCH}"
                echo "${env.GIT_AUTHOR_EMAIL}"
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage('Deploy to stage and Check-style'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        build job: 'deploy-to-staging'
                    }
                }
                stage ('Check Style'){
                    steps {
                        build job: 'check-style'
                    }
                }
            }
        }
        stage ('Deploy to Production'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve PRODUCTION Deployment?'
                }

                build job: 'deploy-to-prod'
            }
            post {
                success {
                    echo 'Code deployed to Production.'
                }

                failure {
                    echo ' Deployment failed.'
                }
            }
        }


    }
}