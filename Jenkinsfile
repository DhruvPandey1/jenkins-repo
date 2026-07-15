pipeline {
    agent any
<<<<<<< HEAD

=======
>>>>>>> 915a1f5 (test deploy)

    environment{
        AWS_REGION='us-east-1'
        ACCOUNT_ID = sh(
            script: "aws sts get-caller-identity --query Account --output text",
            returnStdout: true
        ).trim()
        ECR_REPO="${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/nginx-app"
        ASG_NAME='nginx-app-asg'

    }

    stages {
        stage('Build'){
            steps{
                sh """
                    docker build \
                    -t nginx-image:${BUILD_NUMBER}
                    -t nginx-image:latest .
                """
            }
        }

        stage('Login to ECR'){
            steps{
                sh """
                aws ecr get-login-password --region ${AWS_REGION} \
                | docker login \
                --username AWS \
                --password-stdin \
                ${ECR_REPO}
                """
            }
        }

        stage('Push to ECR repo'){
            steps{
                sh """
                docker tag nginx-image:${BUILD_NUMBER} \
                ${ECR_REPO}:${BUILD_NUMBER}

                docker tag nginx-image:latest \
                ${ECR_REPO}:latest

                docker push ${ECR_REPO}:${BUILD_NUMBER}
                docker push ${ECR_REPO}:latest
                """
            }
        }

        stage('Restarting the instance'){
            steps{
                sh """
                aws autoscaling start-instance-refresh \
                --auto-scaling-group-name ${ASG_NAME}
                """
            }
        }
    }
}