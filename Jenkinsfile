pipeline {
    agent {
        docker {
            image 'hsndocker/aws-cli:latest'
            args "-u root:root --entrypoint=''"
        }
    }
    parameters {
        string(name: 'ACTION', defaultValue: 'apply')
        string(name: 'CLUSTER_NAME', defaultValue: 'engineerx')
        string(name: 'REGION', defaultValue: 'us-east-2')
    }
    environment {
        ACCESS_KEY_ID = credentials('aws-access-key-id')
        SECRET_KEY = credentials('aws-secret-key')
        ACTION = "${params.ACTION}"
        REGION = "${params.REGION}"
        CLUSTER_NAME = "${params.CLUSTER_NAME}"
    }
    stages {
        stage('Providing Access Keys') {
            steps {
                sh('aws configure set aws_access_key_id $ACCESS_KEY_ID')
                sh('aws configure set aws_secret_access_key $SECRET_KEY')
                sh('aws configure set default.region $REGION')
            }
        }
        stage('Setting kubeconfig') {
            steps {
                sh('aws eks --region $REGION update-kubeconfig --name $CLUSTER_NAME')
            }
        }
        stage('Terraform Initialization') {
            steps {
                sh('terraform init')
            }
        }
        stage('Apply Changes') {
            steps {
                script {
                    if (env.ACTION == 'destroy') {
                        sh('terraform destroy --auto-approve')
                    }
                    if (env.ACTION == 'apply') {
                        sh('terraform apply --auto-approve')
                    }
                    if (env.ACTION == 'create') {
                        sh('terraform apply --auto-approve')
                    }
                }
            }
        }
    }
}
