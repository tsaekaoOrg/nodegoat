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
                    credentialsId: 'veracode_login', usernameVariable: 'VERACODE_API_ID', passwordVariable: 'VERACODE_API_KEY') ]) {
                        // fire-and-forget 
                        veracode applicationName: "${VERACODE_APP_NAME}", criticality: 'VeryHigh', debug: true, fileNamePattern: '', pHost: '', pPassword: '', pUser: '', replacementPattern: '', sandboxName: '', scanExcludesPattern: '', scanIncludesPattern: '', scanName: "${BUILD_TAG}", uploadExcludesPattern: '', uploadIncludesPattern: 'upload.zip', useIDkey: true, vid: "${VERACODE_API_ID}", vkey: "${VERACODE_API_KEY}"

                        // wait for scan to complete (timeout: x)
                        //veracode applicationName: "${VERACODE_APP_NAME}", criticality: 'VeryHigh', debug: true, timeout: 20, fileNamePattern: '', pHost: '', pPassword: '', pUser: '', replacementPattern: '', sandboxName: '', scanExcludesPattern: '', scanIncludesPattern: '', scanName: "${BUILD_TAG}", uploadExcludesPattern: '', uploadIncludesPattern: 'upload.zip', useIDkey: true, vid: "${VERACODE_API_ID}", vkey: "${VERACODE_API_KEY}"
                    }      
            }
        }

        stage ('Veracode SCA') {
            steps {
                echo 'Veracode SCA'
                withCredentials([ string(credentialsId: 'SCA_Token', variable: 'SRCCLR_API_TOKEN')]) {
                    //sh "curl -sSL https://download.sourceclear.com/ci.sh | DEBUG=1 sh -s -- scan --no-upload"
                    sh "curl -sSL https://download.sourceclear.com/ci.sh | sh -s -- scan --no-upload"
                }

                withCredentials([ string(credentialsId: 'secret_text', variable: 'MY_SECRET')]) {
                    sh "echo secret=$MY_SECRET"
                }
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
