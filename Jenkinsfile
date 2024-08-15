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
        def nexusUrl = "nexus.harshadevops.site:8081"
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
                    zip -rq backend-${appVersion}.zip * -x Jenkinsfile
                """
            }
        }
        stage('Upload the Artifact to Nexus Repository'){
            steps{
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${nexusUrl}",
                    groupId: 'com.expense',
                    version: "${appVersion}",
                    repository: 'backend',
                    credentialsId: 'nexus-auth',
                    artifacts: [
                        [artifactId: 'backend',
                        classifier: '',
                        file: "backend-${appVersion}.zip",
                        type: 'zip']
                    ]
                )
            }
        }
        stage('Trigger the Deploy Job'){
            steps{
                script{
                    def params = [
                        string(name: 'appVersion', value: "${appVersion}")
                    ]
                    build job: 'backend-cd', parameters: , wait: false
                }
            }
        }
    }
}