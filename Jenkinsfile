pipeline {
    options {
        disableConcurrentBuilds()
    }
    agent none
    triggers {
        pollSCM 'H/2 * * * *'
    }
    stages {
        stage('Plan') {
            agent any
            when {
                branch 'main'
            }
            steps {
                sh 'rm -Rf tools/'
                sh 'mkdir -p tmp tools'
                sh 'curl -o tmp/terraform.zip https://releases.hashicorp.com/terraform/1.0.5/terraform_1.0.5_linux_amd64.zip'
                sh 'unzip tmp/terraform.zip -d tools'
                sh 'chmod u+x tools/terraform'
                sh 'tools/terraform -v'         
                sh 'tools/terraform init'
                sh 'tools/terraform plan -input=false'
            }
        }
        stage('Promote') {
            agent any 
            when {
                branch 'main'
            }
            steps {
                timeout(time: 60, unit: 'MINUTES') {
                    input(id: 'Promotion Gate', message: 'Promote changes?', ok: 'Promote')
                }
            }
        }
        stage('Apply') {
            agent any
            when {
                branch 'main'
            }
            steps {
                sh 'rm -Rf tools/'
                sh 'mkdir -p tmp tools'
                sh 'curl -o tmp/terraform.zip https://releases.hashicorp.com/terraform/1.0.5/terraform_1.0.5_linux_amd64.zip'
                sh 'unzip tmp/terraform.zip -d tools'
                sh 'chmod u+x tools/terraform'
                sh 'tools/terraform -v'         
                sh 'tools/terraform init'
                sh 'tools/terraform apply -input=false -auto-approve'
            }
        }
    }
}
