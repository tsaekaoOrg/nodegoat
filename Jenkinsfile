pipeline {
    agent any

    environment {
        VERACODE_APP_NAME = 'NodeGoat'      // App Name in the Veracode Platform
    }

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
                // zip archive for Veracode scanning.  Only include stuff we need,
                //  aka skip things like node_modules directory
                zip zipFile: 'upload.zip', archive: false, glob: '*.js,*.json,app/**,artifacts/**,config/**'

                echo 'Veracode scanning'
                withCredentials([ usernamePassword ( 
                    credentialsId: 'veracode_login', passwordVariable: 'VERACODE_API_ID', usernameVariable: 'VERACODE_API_KEY') ]) {
                        veracode applicationName: "${VERACODE_APP_NAME}", criticality: 'VeryHigh', debug: true, timeout: 20, fileNamePattern: '', pHost: '', pPassword: '', pUser: '', replacementPattern: '', sandboxName: '', scanExcludesPattern: '', scanIncludesPattern: '', scanName: "${BUILD_TAG}", uploadExcludesPattern: '', uploadIncludesPattern: 'upload.zip', useIDkey: true, vid: "${VERACODE_API_ID}", vkey: "${VERACODE_API_KEY}", vpassword: '', vuser: ''
                    }      
            }
        }

        stage ('Veracode SCA') {
            steps {
                echo 'Veracode SCA'
            }
        }

        stage ('Docker-ize') {
            // when !Windows
            steps {
                echo 'Docker-izing'
            }
        }

        stage ('Deploy') {
            // when !Windows
            steps {
                echo 'Deploying to Heroku'
            }
        }
    }
}
