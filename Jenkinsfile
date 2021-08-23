/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent { label 'GOL' }
    triggers {
        cron('H * * * *')
        // pollSCM('* * * * *')
    }
    parameters {
        string (name: 'BRANCH', defaultValue: 'master', description: 'Branch to build')
        choice(name: 'MAVEN_GOAL', choices: ['package', 'install', 'clean package'], description: 'Pick something')
    }
    options {
        timeout(time: 1, unit: 'HOURS')
        // retry(2) dileep is good boy
    }
    environment {
        CI_ENV = 'DEV'
    }
        stages {
            stage('scm') {
                environment {
                    DUMMY = 'FUN'
                }
                steps {
                    mail subject: "BUILD is started on branch ${'env.GIT_BRANCH'} with build id ${'env.BUILD_ID'}", to: 'devops@dileep.com', from: 'jenkins@dileep.com', body: 'EMPTY BODY'
                    git branch: "${params.BRANCH}", url: 'https://github.com/dileep208/game-of-life.git'
                //input message: 'Continue to the next stage? ', submitter: 'dileepaws, dileepazure'
                    echo env.CI_ENV
                    echo env.DUMMY
                }
            }
            stage('build') {
                steps {
                    // timeout(time: 10, unit: 'MINUTES'){
                        sh " mvn ${params.MAVEN_GOAL} "
                    }
                }
            }
            stage('Ansible'){
              agent {label 'ANSIBLE'}
                steps {
                sh 'cd deployment && ansible-playbook -i hosts deploy.yaml'
                } 
            }

        }
        post {
            success {
                archive '**/gameoflife.war'
                junit '**/TEST-*.xml'
                mail subject: 'BUILD is sucessful'+env.BUILD_ID, to: 'devops@dileep.com', from: 'jenkins@dileep.com', body: 'EMPTY BODY'
            }
            failure{
                mail subject: 'BUILD is failed'+env.BUILD_ID+'url is'+env.BUILD_URL, to: 'devops@dileep.com', from: 'jenkins@dileep.com', body: 'EMPTY BODY'
            }
            always{
                echo "Finished"
            }
            changed{
                echo "changed"
            }
            unstable{
                mail subject: 'BUILD is unstable'+env.BUILD_ID+'url is'+env.BUILD_URL, to: 'devops@dileep.com', from: 'jenkins@dileep.com', body: 'EMPTY BODY'
            }

        }
}
