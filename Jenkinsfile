pipeline {
    agent any

    tools {
        jfrog 'jfrog-cli-01'
    }

    environment {
        REPO_URL = 'https://github.com/ChaManRoRo/manning-jfrog-01.git'
        BRANCH = 'main'
    }

    stages {
        stage('Clone GitHub Repo') {
            steps {
                git branch: "${BRANCH}", url: "${REPO_URL}"
            }
        }

        stage ('Upload Terraform Files to JFrog') {
            steps {
                sh 'echo "Terraform configuration" > main.tf'
                jf 'rt u "*.tf" tf-trial/'
                jf 'rt bp'
            }
        }

        stage('Xray Security Scan') {
            steps {
                jf 'scan --watches Terraform-Security-Watch --fail=true ./'
            }
        }

        stage('Deploy to AWS with Terraform') {
            steps {
                withCredentials([
                    string(credentialsId: 'manning_AWS', variable: 'AWS_ACCESS_KEY_ID'),
                    string(credentialsId: 'manning_AWS', variable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                    export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                    export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    terraform init
                    terraform apply -auto-approve
                    '''
                }
            }
        }
    }
}
