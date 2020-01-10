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

        stage ('Veracode SCA') {
            steps {
                echo 'Veracode SCA'
            }
        }

        stage ('Docker-ize') {
            steps {
                echo 'Docker-izing'
            }
        }

        stage ('Deploy') {
            steps {
                echo 'Deploying to Heroku'
            }
        }
    }
}
