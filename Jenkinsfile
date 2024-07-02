pipeline {
    agent any

    stages {
        stage('Checkout SCM') {
            steps {
                // Descargar el código fuente de GitHub
                git branch: 'develop', url: 'https://github.com/Zephyro-E/repo2.git'
            }
        }
        stage('Deploy') {
            steps {
                sh'''
                    sam deploy --stack-name todo-list-aws-prod --region us-east-1 --s3-bucket s3tarea14b --config-file samconfig.yaml
                '''
            }
        }
        stage('CatchUrls') {
            steps {
                sh 'aws cloudformation describe-stacks --stack-name todo-list-aws-prod --region us-east-1 --query "Stacks[0].Outputs[?OutputKey==\'BaseUrlApi\' || OutputKey==\'DeleteTodoApi\' || OutputKey==\'ListTodosApi\' || OutputKey==\'UpdateTodoApi\' || OutputKey==\'GetTodoApi\' || OutputKey==\'CreateTodoApi\'].OutputValue" --output json > deploy_output.json'
            }
        }
        stage('Unit') {
            steps {
                sh'''
                    export PYTHONPATH=%WORKSPACE%
                    pytest --junitxml=result-unit.xml test/unit
                '''
            }
        }
    }
}
