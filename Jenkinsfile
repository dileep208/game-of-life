/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent { label 'GOL' }
    triggers {
        cron('H * * * *')
        pollSCM('* * * * *')
    }
    parameters {
        string (name: 'BRANCH', defaultValue: 'master', description: 'Branch to build')
        choice(name: 'MAVEN_GOAL', choices: ['package', 'install', 'clean package'], description: 'Pick something')
    }
    options {
        timeout(time: 1, unit: 'HOURS')
        retry(2)
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

                    git branch: "${params.BRANCH}", url: 'https://github.com/dileep208/game-of-life.git'
                //input message: 'Continue to the next stage? ', submitter: 'dileepaws, dileepazure'
                    echo env.CI_ENV
                    echo env.DUMMY
                }
            }
            stage('build') {
                steps {
                    timeout(time: 10, unit: 'MINUTES'){
                        sh " mvn ${params.MAVEN_GOAL} "
                    }
                }
            }
        }
        post {
            success {
                archive '**/gameoflife.war'
                junit '**/TEST-*.xml'
            }
        }
}
