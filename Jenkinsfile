pipeline{
    agent any
    stages{
        stage('git checkout'){
            steps{
               checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'GitCreds', url: 'https://github.com/Subhash-Rokkala/war-web-project.git']])
            }
        }
        stage('build process'){
            steps{
                sh 'mvn clean install'
            }
        }
    }
}
