node{
    stage('SCM Checkout'){
        git 'https://github.com/dileep208/game-of-life.git'
    }
    stage('Compile-Package'){
        sh 'mvn package'
    }
}