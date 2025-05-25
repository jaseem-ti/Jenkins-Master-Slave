// declarative pipeline
def VERSION = '1.0.0'

pipeline {
    agent none
   
    environment {
        PROJECT = "Jenkins project"
        AZ_SUB_ID = "your-subscription-id"
        AZ_TEN_ID = "your-tenant-id"
    }
    stages {
        stage('Dev Tools Verification') {
            when {
                branch 'development'
            }
            agent { label 'DEV' }
            steps {
                sh "mvn --version"
                sh "java --version"
                sh "terraform --version"
                sh "packer --version"
                sh "trivy --version"
            }
        }       

//-------------------PRODUCTION STAGE-------------------
   stages {
        stage('PROD Tools Verification') {
            when {
                branch 'production'
            }
            agent { label 'PROD' }
            steps {
                sh "mvn --version"
                sh "java --version"
                sh "terraform --version"
                sh "packer --version"
                sh "trivy --version"
            }
       
        }
    }
}
}