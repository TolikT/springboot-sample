pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    stages {
        stage('checkout scm') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'git@github.com:TolikT/springboot-sample.git']]])
            }
        }
        stage('build and publish') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus', passwordVariable: 'NEXUS_PASSWORD', usernameVariable: 'NEXUS_LOGIN')]) {
                    sh encoding: 'UTF-8', script: "./gradlew clean publish -Pversion=${BRANCH_NAME}.${BUILD_NUMBER} --no-daemon -s"
                }
            }
        }
        stage('sonarqube') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'sonarqube', passwordVariable: 'SONAR_PASSWORD', usernameVariable: 'SONAR_LOGIN')]) {
                    sh encoding: 'UTF-8', script: "./gradlew sonarqube -Pversion=${BRANCH_NAME}.${BUILD_NUMBER} --no-daemon -s"
                }
            }
        }
    }
    post {
        always {
            cleanWs(
                cleanWhenAborted: true,
                cleanWhenFailure: true,
                cleanWhenNotBuilt: true,
                cleanWhenSuccess: true,
                cleanWhenUnstable: true,
                deleteDirs: true
            )
        }
    }
}
