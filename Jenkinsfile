pipeline {
    agent { label 'JAVA' }

    environment {
        IMAGE_NAME = "nginx"
        TAG_NAME = "1.29"
        ECR_REPO = "928102491225.dkr.ecr.ap-south-1.amazonaws.com/dev/spcimage"
        AWS_REGION = "ap-south-1"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build App') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${TAG_NAME} ."
            }
        }

        stage('Trivy Scan') {
            steps {
                sh "trivy image ${IMAGE_NAME}:${TAG_NAME}"
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                aws ecr get-login-password --region ${AWS_REGION} | \
                docker login --username AWS --password-stdin 928102491225.dkr.ecr.ap-south-1.amazonaws.com
                """
            }
        }

        stage('Tag Image') {
            steps {
                sh "docker tag ${IMAGE_NAME}:${TAG_NAME} ${ECR_REPO}:latest"
            }
        }

        stage('Push to ECR') {
            steps {
                sh "docker push ${ECR_REPO}:latest"
            }
        }
    }

    // post {
    //     always {
    //         archiveArtifacts artifacts: '**/*.jar'
    //         junit '**/surefire-reports/*.xml'
    //     }
    }
