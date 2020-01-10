pipeline {
    agent any

    stages{
        stage ('config verify') {
            steps {
                sh 'pwd'
                sh 'ls -l'
            }
        }

        stage ('build') {
            steps {
                // use the NodeJS plugin
                nodejs(nodeJSInstallationName: 'NodeJS-12.0.0') {
                    sh 'npm config ls'
                    sh 'npm --version'
                    sh 'npm install'
                }
            }
        }

        stage ('Veracode scan') {
            steps {
                sh 'echo Veracode scanning'
               //withCredentials([ usernamePassword ( 
               //     credentialsId: 'veracode_login', passwordVariable: 'VERACODE_PASSWORD', usernameVariable: 'VERACODE_USERNAME') ]) {
               //     veracode applicationName: 'SonarQube plugin', criticality: 'VeryHigh', fileNamePattern: '', pHost: '', pPassword: '', pUser: '', replacementPattern: '', sandboxName: '', scanExcludesPattern: '', scanIncludesPattern: '', scanName: "Jenkins pipeline (${veracodeBuildNumber})", uploadExcludesPattern: '', uploadIncludesPattern: '**/target/*.jar', useIDkey: true, vid: "${VERACODE_USERNAME}", vkey: "${VERACODE_PASSWORD}", vpassword: '', vuser: ''
               //     }      
            }
        }
    }
}

/*
// env vars
buildNumber = "${env.BUILD_NUMBER}"

// run on any node
node {
    final scmVars = checkout(scm)
    //echo "scmVars: ${scmVars}"
    buildVer = scmVars.GIT_COMMIT.substring(0,7)    // first 6 chars to match the short form
    echo "last commit git hash (short) = ${buildVer}"
    veracodeBuildNumber = "${buildNumber}-${buildVer}"
    echo "Veracode build ID = ${veracodeBuildNumber}"

    stage ('build') {
        //git url: 'file:///Users/krise/my-repositories/sonarqube-veracode'

        // mvnHome from 'mvn --version', don't forget the /bin at the end
        // future, use the tools capability of Pipelines
        //def mvnHome = '/usr/local/Cellar/maven/3.5.3/libexec/bin'
        sh "npm install"
    }

    stage ('upload-scan') {

        
        withCredentials([ usernamePassword ( 
            credentialsId: 'veracode_login', passwordVariable: 'VERACODE_PASSWORD', usernameVariable: 'VERACODE_USERNAME') ]) {
            veracode applicationName: 'SonarQube plugin', criticality: 'VeryHigh', fileNamePattern: '', pHost: '', pPassword: '', pUser: '', replacementPattern: '', sandboxName: '', scanExcludesPattern: '', scanIncludesPattern: '', scanName: "Jenkins pipeline (${veracodeBuildNumber})", uploadExcludesPattern: '', uploadIncludesPattern: '**/target/*.jar', useIDkey: true, vid: "${VERACODE_USERNAME}", vkey: "${VERACODE_PASSWORD}", vpassword: '', vuser: ''
        }
    }
}
*/