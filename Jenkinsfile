pipeline {
    agent any

    environment {
        VERACODE_APP_NAME = 'NodeGoat'      // App Name in the Veracode Platform
    }

    // this is optional on Linux, if jenkins does not have access to your locally installed docker
    //tools {
        // these match up with 'Manage Jenkins -> Global Tool Config'
        //'org.jenkinsci.plugins.docker.commons.tools.DockerTool' 'docker-latest' 
    //}

    options {
        // only keep the last x build logs and artifacts (for space saving)
        buildDiscarder(logRotator(numToKeepStr: '20', artifactNumToKeepStr: '20'))
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
                        bat 'echo %PATH%'
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

                script {
                    if(isUnix() == true) {
                        env.HOST_OS = 'Unix'
                    }
                    else {
                        env.HOST_OS = 'Windows'
                    }
                }

                echo 'Veracode scanning'
                withCredentials([ usernamePassword ( 
                    credentialsId: 'veracode_login', usernameVariable: 'VERACODE_API_ID', passwordVariable: 'VERACODE_API_KEY') ]) {
                        // fire-and-forget 
                        veracode applicationName: "${VERACODE_APP_NAME}", criticality: 'VeryHigh', debug: true, fileNamePattern: '', pHost: '', pPassword: '', pUser: '', replacementPattern: '', sandboxName: '', scanExcludesPattern: '', scanIncludesPattern: '', scanName: "${BUILD_TAG}-${env.HOST_OS}", uploadExcludesPattern: '', uploadIncludesPattern: 'upload.zip', useIDkey: true, vid: "${VERACODE_API_ID}", vkey: "${VERACODE_API_KEY}"

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
                        script {
                            if(isUnix() == true) {
                                sh "curl -sSL https://download.sourceclear.com/ci.sh | sh"

                                // debug, no upload
                                //sh "curl -sSL https://download.sourceclear.com/ci.sh | DEBUG=1 sh -s -- scan --no-upload"
                            }
                            else {
                                // allow-dirty since I was in the middle of updating the readme files,
                                //      really not needed
                                powershell '''
                                            Set-ExecutionPolicy AllSigned -Scope Process -Force
                                            iex ((New-Object System.Net.WebClient).DownloadString('https://download.srcclr.com/ci.ps1'))
                                            srcclr scan --allow-dirty
                                            '''
                            }
                        }
                    }
                }
            }
        }

        // only works on *nix, as we're building a Linux image
        //  uses the natively installed docker
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
