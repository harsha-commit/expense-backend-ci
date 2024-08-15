pipeline{
    agent{
        label 'AGENT-1'
    }

    options{
        timeout(time: 45, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    environment{
        def appVersion = ""
    }

    stages{
        stage('Read the Application Version'){
            steps{
                script{
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                }
            }
        }
        stage('Install the Dependencies'){
            steps{
                sh """
                    npm install
                    echo "Application Version: ${appVersion}"
                """
            }
        }
        stage('Build the Artifact'){
            steps{  
                sh """
                    rm -rf backend-${appVersion}.zip
                    zip -rq backend-${appVersion}.zip -x Jenkinsfile
                """
            }
        }
    }

    post{
        always{
            deleteDir()
        }
    }
}