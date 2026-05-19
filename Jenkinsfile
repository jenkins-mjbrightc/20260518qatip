pipeline {
  agent any
  environment {
    AWS_DEFAULT_REGION="us-west-2"
  }
    stages {
      stage('Clone Repository') {
        steps {
          git branch: 'main', url: 'https://github.com/jenkins-mjbrightc/20260518qatip'
        }
      }
      stage('Install Terraform') {
        steps {
          sh '''
               wget https://releases.hashicorp.com/terraform/1.5.6/terraform_1.5.6_linux_amd64.zip
               unzip -o terraform_1.5.6_linux_amd64.zip
               sudo mv terraform /usr/local/bin/
               terraform --version
               '''
        }
      }
      stage('Terraform Init') {
        steps {
          withCredentials([usernamePassword(credentialsId: 'aws_user', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
          sh '''
                   export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                   export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                   export AWS_DEFAULT_REGION=$AWS_DEFAULT_REGION
                   terraform init
                   '''
          }
        }
      }
      stage('Terraform Plan') {
        steps {
          withCredentials([usernamePassword(credentialsId: 'aws_user', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
sh '''
                   export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                   export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                   terraform plan
                   '''
          }
      }
    }
    stage('Terraform Apply') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'aws_user', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
        sh '''
                   export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                   export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                   terraform apply -auto-approve
                   '''
        }
      }
    }
  }
}

