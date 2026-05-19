pipeline {
    agent any
    environment {
        AWS_TEMP_CREDS = credentials('aws_user_joined')
    }
    stages {
      stage('Use AWS') {
        steps {
            script {
                def parts = AWS_TEMP_CREDS.split('\\|')
                env.AWS_ACCESS_KEY_ID = (parts[0]).substring(1, length(parts[0]) )
                env.AWS_SECRET_ACCESS_KEY = parts[1]
                env.AWS_SESSION_TOKEN = parts[2]
            }
            sh 'env | grep AWS_'
            // sh 'aws sts get-caller-identity'
         }
       }

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
