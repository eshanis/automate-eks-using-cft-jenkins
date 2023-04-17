pipeline {
    agent any
    stages {
        stage('Automate EKS') {
            steps {
            sh "aws cloudformation create-stack --stack-name eshani-EKS-node14 --template-body file://automate-eks.yaml --region 'us-east-1'"
              }
         }


     }
}
