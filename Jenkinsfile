pipeline {

    agent any

    environment {
        TF_IN_AUTOMATION = "true"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {

            steps {

                withCredentials([
                    string(credentialsId: 'AZURE_CLIENT_ID', variable: 'ARM_CLIENT_ID'),
                    string(credentialsId: 'AZURE_CLIENT_SECRET', variable: 'ARM_CLIENT_SECRET'),
                    string(credentialsId: 'AZURE_SUBSCRIPTION_ID', variable: 'ARM_SUBSCRIPTION_ID'),
                    string(credentialsId: 'AZURE_TENANT_ID', variable: 'ARM_TENANT_ID')
                ]) {

                    sh '''
                    terraform plan \
                      -var="subscription_id=$ARM_SUBSCRIPTION_ID" \
                      -var="resource_group_name=rg-devops-demo" \
                      -var="location=Central India" \
                      -out=tfplan
                    '''
                }
            }
        }

        stage('Terraform Apply') {

            steps {

                input message: 'Approve Terraform Deployment?'

                withCredentials([
                    string(credentialsId: 'AZURE_CLIENT_ID', variable: 'ARM_CLIENT_ID'),
                    string(credentialsId: 'AZURE_CLIENT_SECRET', variable: 'ARM_CLIENT_SECRET'),
                    string(credentialsId: 'AZURE_SUBSCRIPTION_ID', variable: 'ARM_SUBSCRIPTION_ID'),
                    string(credentialsId: 'AZURE_TENANT_ID', variable: 'ARM_TENANT_ID')
                ]) {

                    sh '''
                    terraform apply \
                      -auto-approve tfplan
                    '''
                }
            }
        }
    }

    post {

        success {
            echo 'Azure Infrastructure Created Successfully'
        }

        failure {
            echo 'Terraform Deployment Failed'
        }
    }
}
