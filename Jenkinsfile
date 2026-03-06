pipeline {
    agent {label 'SPC'}
    triggers {
        pollSCM('* * * * *')
    }
    stages { 
        stage ('git checkout') { 
            steps { 
                git url: 'https://github.com/rbhanuprasadreddy6-cloud/spring-petclinic.git',
                    branch: 'main'
            }
        }
        stage ('build and scan') { 
            steps { 
                withCredentials([string(credentialsId: 'sonar_id', variable: 'SONAR_TOKEN')]) {
                withSonarQubeEnv("SONAR") { 
                    sh """mvn package sonar:sonar \
                        -Dsonar.projectKey=rbhanuprasadreddy6-cloud_spring-petclinic \
                        -Dsonar.organization=rbhanuprasadreddy6-cloud \
                        -Dsonar.host.url=https://sonarcloud.io \
                        -Dsonar.login=$SONAR_TOKEN """
                    }
                }
            }
        }
    } 
    stage('Binary File Store') {
            steps {
                rtUpload(
                    serverId: 'JFROG',
                    spec: '''{
                        "files": [
                            {
                                "pattern": "target/*.jar",
                                "target": "spcjava-spc/"
                            }
                        ]
                    }'''
                )
                rtPublishBuildInfo(serverId: 'JFROG')
            }
        }
    

    post {
        always {
            archiveArtifacts artifacts: '**/*.jar'
            junit '**/surefire-reports/*.xml'
        }
    }
}    
    