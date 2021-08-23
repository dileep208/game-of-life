pipeline {

    agent { label 'GOL'}
    triggers {
        cron('H * * * *')
        pollSCM('* * * * *')
    }
    parameters {
        string(name: 'BRANCH', defaultValue: 'master', description: 'Branch to build' )
        choice(name: 'GOAL', choices: ['package', 'clean package', 'install'], description: 'maven goals')
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
                mail subject: 'BUILD Started '+env.BUILD_ID, to: 'devops@qt.com', from: 'jenkins@qt.com', body: 'EMPTY BODY'
                git branch: "${params.BRANCH}", url: 'https://github.com/asquarezone/game-of-life.git'
                //input message: 'Continue to next stage? ', submitter: 'qtaws,qtazure'
                echo env.CI_ENV
                echo env.DUMMY
            }
        }
        stage('build') {
            steps {
                echo env.GIT_URL
                timeout(time:10, unit: 'MINUTES') {
                    sh "mvn ${params.GOAL}"
                }
                
            }
        }
        stage('Ansible') {
            agent { label 'ANSIBLE'}
            steps{
                // requires SonarQube Scanner for Maven 3.2+
                    sh 'cd deployment && ansible-playbook -i hosts deploy.yaml'
                }
            }
        
    }
    post {
        success {
            archive '**/gameoflife.war'
            junit '**/TEST-*.xml'
            mail subject: 'BUILD Completed Successfully '+env.BUILD_ID, to: 'devops@qt.com', from: 'jenkins@qt.com', body: 'EMPTY BODY'
        }
        failure {
            mail subject: 'BUILD Failed '+env.BUILD_ID+'URL is '+env.BUILD_URL, to: 'devops@qt.com', from: 'jenkins@qt.com', body: 'EMPTY BODY'
        }
        always {
            echo "Finished"
        }
        changed {
            echo "Changed"
        }
        unstable {
            mail subject: 'BUILD Unstable '+env.BUILD_ID+'URL is '+env.BUILD_URL, to: 'devops@qt.com', from: 'jenkins@qt.com', body: 'EMPTY BODY'
            // for unstable
        }
        
    }
}