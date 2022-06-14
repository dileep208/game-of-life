node('any'){
    stage('SCM'){
        git 'https://github.com/dileep208/game-of-life.git'
    }
    stage('BUILD'){
        sh 'mvn clean package'
    }
    stage('POST BUILD'){
        junit '**/TEST-*.xml'
        archive '**/*.war'
    }
}