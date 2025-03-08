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

        stage('Upload Terraform Files to JFrog') {
            steps {
                jf 'rt u "*.tf" tf-trial/'  // Upload all Terraform files to JFrog
                jf 'rt bp'  // Publish build info to JFrog
            }
        }

        stage('Xray Security Scan') {
            steps {
                jf 'scan --watches Terraform-Security-Watch --fail=true ./'  // Run JFrog security scan
            }
        }

        stage('Deploy to AWS with Terraform') {
            steps {
                withCredentials([
                    aws(credentialsId: 'manning_AWS', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')
                ]) {
                    sh '''
                    echo Deploying to AWS...
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
