pipeline{
    agent {label 'master'}
    tools { 
        maven 'MAVEN_HOME' 
    }
    stages{
        stage('SCM-Checkout'){
            steps{
                git 'https://github.com/arvind61h/openmrs-core.git'
            }
        }
        stage("Build-Artifacts"){
            steps{
                
                sh 'mvn install -DskipTests'
            }
            post {
                success{
                    junit 'web/target/surefire-reports/*.xml'
                }
            }
        }
        stage("Archiving-Artifcats"){
            steps{
                archiveArtifacts artifacts: '**/*.jar, *.war', followSymlinks: false
            }
        }
        stage("Static-code-Analysis"){
            steps{
                sh 'mvn sonar:sonar'
                }
            post{
                always{
                    jacoco buildOverBuild: true, changeBuildStatus: true, deltaBranchCoverage: '30', deltaClassCoverage: '30', deltaComplexityCoverage: '30', deltaInstructionCoverage: '30', deltaLineCoverage: '30', deltaMethodCoverage: '30', maximumBranchCoverage: '25', maximumClassCoverage: '25', maximumComplexityCoverage: '25', maximumInstructionCoverage: '25', maximumLineCoverage: '25', maximumMethodCoverage: '25', minimumBranchCoverage: '25', minimumClassCoverage: '25', minimumComplexityCoverage: '25', minimumInstructionCoverage: '25', minimumLineCoverage: '25', minimumMethodCoverage: '25'
                }
            }
        }
        stage("JOb Status Noitifaction"){
            steps{
                slackSend channel: '﻿openmrs', message: 'started ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)'
            }
        }
        stage('Deploy-artifacts'){
            steps{
                mvn deploy
            }
        }
        stage("Deploying on Dev Environment"){
            steps{
                deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://3.22.221.114:8080/')], contextPath: 'openmrs', war: '**/*.war'
            }
            post{
                always{
                    slackSend channel: 'openmrs', color: '#439FE0', message: '"Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"', teamDomain: 'testing-6yw5886', tokenCredentialId: 'slack'
                }
            }
        }
    }
}