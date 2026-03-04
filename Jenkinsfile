pipeline {
    agent { label 'kavya' }

    environment { 
        project = 'expense'
        component = 'backend' 
        region = "us-east-1"
        appVersion = ''
    }

    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }

    parameters{
        string(name: 'version', description: 'Enter the application version')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'Pick environment')
    }

    stages {

        stage('Setup Environment'){
            steps{
                script{
                    env.appVersion = params.version
                    env.targetEnv = params.deploy_to
                }
            }
        }

        stage('Deploy') {
    steps {
        script{
            withAWS(region: env.region, credentials: "aws-creds") {
                sh """
                aws eks update-kubeconfig --region ${env.region} --name expense-${env.targetEnv}
                kubectl get nodes

                cd helm
                sed -i 's/IMAGE_VERSION/${env.appVersion}/g' values-${env.targetEnv}.yaml
                cat values-${env.targetEnv}.yaml
                cd backend-deploy/helm
                            sed -i 's/IMAGE_VERSION/${params.version}/g' values-${env.targetENV}.yaml
                            helm upgrade --install $COMPONENT -n $PROJECT -f values-${env.targetENV}.yaml .
                """
            }
        }
    }
}
    }

    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        failure { 
            echo 'Pipeline failed'
        }
        success { 
            echo 'Pipeline succeeded'
        }
    }
}