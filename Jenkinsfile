pipeline {
    agent {
        label 'AGENT-1'
    }
    options{
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        //retry(1)
    }
    parameters{
        booleanParam(name: 'deploy', defaultValue: false, description: 'Select to deploy or not')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'qa', 'uat', 'pre-prod', 'prod'], description: 'Select your Environment')
        string(name: 'version', choices: 'Enter your application version')
    }
    environment {
        appVersion = '' // this will become global, we can use across pipeline
        region = 'us-east-1'
        account_id = '203918861424'
        project = 'expense'
        environment = ''
        component = 'backend'
    }

    stages {

        stage('Setup Environment'){
            steps{
                script{
                    envinment = params.ENVIRONMENT
                    appVersion = params.version
                }
            }

        }
        stage('Deploy'){
            when {
                expression { params.deploy }
            }
            steps{
                steps{
                    withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                        sh """
                            aws eks update-kubeconfig --region ${region} --name ${project}-${environment}
                            cd helm
                            sed -i 's/IMAGE_VERSION/${appVersion}/g' values-${environment}.yaml
                            helm upgrade --install ${component} -n ${project} -f values-${environment}.yaml .
                        """
                    }
                }
            }
        }
    }

    post {
        always{
            echo "This sections runs always"
            deleteDir()
        }
        success{
            echo "This section run when pipeline success"
        }
        failure{
            echo "This section run when pipeline failure"
        }
    }
}