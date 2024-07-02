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
            }
        }
    }
}
