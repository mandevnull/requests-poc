pipeline {
    agent {
        docker {
            image 'python:3.11'
            args '-u root' // si necesitas permisos para instalar cosas
        }
    }

    environment {
        PIP_DISABLE_PIP_VERSION_CHECK = 1
        PYTHONDONTWRITEBYTECODE = 1
        PIP_NO_CACHE_DIR = off
    }

    stages {
        stage('Instalar dependencias') {
            steps {
                sh '''
                    pip install --upgrade pip
                    pip install -r requirements.txt || true
                    pip install -e .[socks] pytest pytest-cov flake8
                '''
            }
        }

        stage('Lint') {
            steps {
                sh 'flake8 requests'
            }
        }

        stage('Tests') {
            steps {
                sh 'pytest --cov=requests tests/'
            }
        }

        stage('Empaquetado') {
            steps {
                sh 'tar czf requests_package.tar.gz requests'
            }
        }

        stage('Simular despliegue') {
            steps {
                sh '''
                    mkdir -p staging
                    cp requests_package.tar.gz staging/
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/requests_package.tar.gz', fingerprint: true
        }
    }
}

