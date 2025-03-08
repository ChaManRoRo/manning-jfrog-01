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
    }
}
