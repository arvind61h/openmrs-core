pipeline{
    agent any
    tools { 
        maven 'MAVEN_HOME' 
        jdk 'JDK'
    }
    stages{
        stage('SCM-Checkout'){
            steps{
                git 'https://github.com/arvind61h/openmrs-core.git'
            }
        }
        stage("Build-Artifacts"){
            steps{
                
                sh 'mvn clean install'
            }
            post {
                success{
                    junit 'webapp/target/test-reports/*.xml'
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
                success{
                    jacoco buildOverBuild: true, changeBuildStatus: true, deltaBranchCoverage: '80', deltaClassCoverage: '80', deltaComplexityCoverage: '80', deltaInstructionCoverage: '80', deltaLineCoverage: '80', deltaMethodCoverage: '80', maximumBranchCoverage: '80', maximumClassCoverage: '80', maximumComplexityCoverage: '80', maximumInstructionCoverage: '80', maximumLineCoverage: '80', maximumMethodCoverage: '80', minimumBranchCoverage: '80', minimumClassCoverage: '80', minimumComplexityCoverage: '80', minimumInstructionCoverage: '80', minimumLineCoverage: '80', minimumMethodCoverage: '80'   
                }
            }
        }
        stage("JOb Status Noitifaction"){
            steps{
                slackSend channel: 'openmrs', color: '#439FE0', message: '"Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"', teamDomain: 'testing-6yw5886', tokenCredentialId: 'slack'
            }
        }
        stage('Deploy-artifacts'){
            steps{
                mvn deploy
            }
        }
        stage("Deploying on Dev Environment"){
            steps{
                deploy adapters: [tomcat9(credentialsId: 'tomcat', path: '', url: 'http://3.128.189.105:8080/')], contextPath: 'openmrs', war: '**/*.war'
            }
            post{
                always{
                    slackSend channel: 'openmrs', color: '#439FE0', message: '"Build Started: ${env.JOB_NAME} ${env.BUILD_NUMBER}"', teamDomain: 'testing-6yw5886', tokenCredentialId: 'slack'
                }
            }
        }
    }
}