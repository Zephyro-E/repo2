pipeline {
    agent any

    stages {
        stage('Checkout SCM') {
            steps {
                // Descargar el código fuente de GitHub
                git branch: 'develop', url: 'https://github.com/Zephyro-E/repo2.git'
            }
        }
        stage('Static test') {
            steps {
                // Flake8 sobre el código descargado en la etapa anterior
                sh'''
                    export PYTHONPATH=%WORKSPACE%
                    sudo -u ubuntu /home/ubuntu/.local/bin/flake8 --exit-zero src >flake8.out
                '''
                 recordIssues tools: [flake8(name: 'flake8', pattern: 'flake8.out')], qualityGates: [[threshold:8, type: 'TOTAL', unstable: true], [threshold: 10, type: 'TOTAL', unstable: false]]

                sh'''
                    sudo -u ubuntu /home/ubuntu/.local/bin/bandit --exit-zero -r ./src -f custom -o bandit.out --severity-level medium --msg-template "{abspath}:{line}: [{test_id}] {msg}"
                '''
                    recordIssues tools: [pyLint(name: 'Bandit', pattern: 'bandit.out')], qualityGates: [[threshold:2, type: 'TOTAL', unstable: true], [threshold: 4, type: 'TOTAL', unstable: false]]
                sh'''
                    sudo -u ubuntu chmod 755 /home/ubuntu

                '''    
            }
        }
        stage('Deploy') {
            steps {
                sh'''
                    sam deploy --stack-name todo-list-aws --region us-east-1 --s3-bucket s3tarea14 --config-file samconfig.yaml
                '''
            }
        }
        stage('CatchUrls') {
            steps {
                aws cloudformation describe-stacks --stack-name todo-list-aws --region us-east-1 --query "Stacks[0].Outputs[?OutputKey=='BaseUrlApi' || OutputKey=='DeleteTodoApi' || OutputKey=='ListTodosApi' || OutputKey=='UpdateTodoApi' || OutputKey=='GetTodoApi' || OutputKey=='CreateTodoApi'].OutputValue" --output json > deploy_output.json
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
        stage('StackDelete') {
            steps {
                aws cloudformation delete-stack --stack-name todo-list-aws --region us-east-1
            }
        }
    }
}
