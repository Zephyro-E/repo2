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
                    '/home/ubuntu/.local/bin/flake8/flake8 --exit-zero --format=pylint src >flake8.out'
                '''
                 recordIssues tools: [flake8(name: 'flake8', pattern: 'flake8.out')], qualityGates: [[threshold:8, type: 'TOTAL', unstable: true], [threshold: 10, type: 'TOTAL', unstable: false]]
            }
        }
    }
}
