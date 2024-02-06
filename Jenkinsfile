pipeline {
    agent { node { label 'AGENT-1' } }
    // options {
    //     ansiColor('xterm')
    // }
    options {
        ansiColor('xterm')
    }
    parameters {
        string(name: 'version', defaultValue: '1.0.0', description: 'Which version to Deploy')
        string(name: 'environment', defaultValue: 'dev', description: 'Which environment to Deploy')
        booleanParam(name: 'Destroy', defaultValue: false, description: 'Toggle this value')
        booleanParam(name: 'Deploy', defaultValue: false, description: 'Toggle this value')
    }
    stages {
        stage('Deploy'){
            steps{
                echo "Deploying..."
                echo "Version from params: ${params.version}"

            }
        }
        stage('Init'){
            steps{
                sh """
                cd terraform
                terraform init -reconfigure
                """
            }
        }
        stage('Plan'){
            steps{
                sh """
                cd terraform
                terraform plan  -var="app_version=${params.version}" -var="environment=${params.environment}"
                """
            }
        }
        stage('Apply') {
            when{
                expression { 
                   params.Deploy
                }
            }
            // input {
            //     message "Should we continue?"
            //     ok "Yes, we should."
            //     submitter "alice,bob"
            //     parameters {
            //         string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
            //     }
            // }
            steps {
                sh """
                cd terraform
                terraform apply -var-file=${params.environment}/${params.environment}.tfvars -var="app_version=${params.version}" -var="environment=${params.environment}" -auto-approve
                """
            }
        }
        stage('Destory DEV'){
            when{
                expression { 
                    return params.environment == 'dev' && params.Destroy 
                }
            }
            // input {
            //     message "Should we continue?"
            //     ok "Yes, we should."
            //     submitter "alice,bob"
            //     parameters {
            //         string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')
            //     }
            // }
            steps{
                sh """
                cd terraform
                terraform destroy  -var="app_version=${params.version}" -var="environment=${params.environment}" -auto-approve
                """
            }
        }
    }


    post{
        always{
            echo 'cleaning up workspace'
            deleteDir()
        }
    }
}