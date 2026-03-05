pipeline {
    agent { label 'kavya' }

    environment { 
        project = 'expense'
        component = 'backend'
        region = "us-east-1"
        appVersion = ''
        environment = ''
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
                    appVersion = params.version
                    environment = params.deploy_to
                }
            }
        }

        stage('Deploy') {
            steps {
                script{
                    withAWS(region: 'us-east-1', credentials: "aws-creds-${environment}") {
                        sh """
                            aws eks update-kubeconfig --region ${region} --name expense-${environment}
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml
                            helm upgrade --install ${component} -n ${project} -f values-${environment}.yaml .
                        """
                    }
                }
            }
        }

    }

    post{
        always{
            echo 'I will always say Hello again!'
            deleteDir()
        }
        failure{
            echo 'Pipeline failed'
        }
        success{
            echo 'Pipeline succeeded'
        }
    }
}