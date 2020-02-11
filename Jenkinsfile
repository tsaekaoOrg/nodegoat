pipeline {
    agent any

    environment {
        VERACODE_APP_NAME = 'NodeGoat'      // App Name in the Veracode Platform
    }

    //tools {
        // these match up with 'Manage Jenkins -> Global Tool Config'
        //'org.jenkinsci.plugins.docker.commons.tools.DockerTool' 'docker-latest' 
    //}

    options {
        // only keep the last 30 build logs and artifacts (for space saving)
        buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '30'))
    }

    stages{
        stage ('environment verify') {
            steps {
                script {
                    if (isUnix() == true) {
                        sh 'pwd'
                        sh 'ls -la'
                        sh 'echo $PATH'
                    }
                    else {
                        bat 'dir'
                        bat 'set'
                    }
                }
            }
        }

        stage ('build') {
            steps {
                // use the NodeJS plugin
                nodejs(nodeJSInstallationName: 'NodeJS-12.0.0') {
                    script {
                        if(isUnix() == true) {
                            //sh 'npm config ls'
                            sh 'npm --version'
                            sh 'npm install'
                        }
                        else {
                            bat 'npm --version'
                            bat 'npm install'  
                        }
                    }

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
                    nodejs(nodeJSInstallationName: 'NodeJS-12.0.0') {
                        sh "curl -sSL https://download.sourceclear.com/ci.sh | sh"

                        // debug, no upload
                        //sh "curl -sSL https://download.sourceclear.com/ci.sh | DEBUG=1 sh -s -- scan --no-upload"
                    }
                }
            }
        }

        // only works on *nix, as we're building a Linux image
        stage ('Deploy') {
            when { expression { return (isUnix() == true) } }
            steps {
                echo 'building Docker image'
                sh 'docker version'

                ansiColor('xterm') {
                    sh 'docker build -t nodegoat:${BUILD_TAG} .'
                }
                
                // split into separate stage??
                echo 'Deploying ...'
        
            }
        }
    }
}
