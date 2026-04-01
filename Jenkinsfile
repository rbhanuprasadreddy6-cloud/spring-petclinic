pipeline {
    agent { label 'JAVA' }
    environment {
        image_name = "nginx"
        tag_name = "1.29"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        // stage('git url'){
        //     steps{
        //         sh ''' git clone "https://github.com/Sampathgoud20/spring-petclinic.git" '''
        //     }
        // }
        

        // stage('Build & Sonar Scan') {
        //     steps {
        //         withCredentials([
        //             string(credentialsId: 'sonar-id', variable: 'SONAR_TOKEN')
        //         ]) {
        //             withSonarQubeEnv('SONAR') {
        //                 sh '''
        //                 mvn clean package sonar:sonar \
        //                   -Dsonar.projectKey=gorigemadhu085-cmd_spring-petclinic \
        //                   -Dsonar.organization=gorigemadhu085-cmd-1 \
        //                   -Dsonar.host.url=https://sonarcloud.io \
        //                   -Dsonar.login=$SONAR_TOKEN
        //                 '''
        //             }
        //         }
        //     }
        // }
      stage('Build App') {
         steps {
             sh 'mvn clean package -DskipTests'
           }
     }
      stage("docker image build" ){
        steps{
            sh """ docker image build -t ${image_name}:${tag_name} . """
     }
      }
      stage("trivy scan image push to ecr"){
        steps{
            sh """ aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 928102491225.dkr.ecr.ap-southeast-1.amazonaws.com && \
                   trivy image ${image_name}:${tag_name}  && \
                   docker tag ${image_name}:${tag_name} 928102491225.dkr.ecr.ap-southeast-1.amazonaws.com/dev/spcimage:latest && \
                  docker push 928102491225.dkr.ecr.ap-southeast-1.amazonaws.com/dev/spcimage:latest """
        }

      }
    
    }

// post {
//         always{
//             archiveArtifacts artifacts: '**/*.jar'
//             junit '**/surefire-reports/*.xml'

//         }
//     }
}