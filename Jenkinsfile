# pipeline
pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
    }

    stages {
        stage('Clonar repositorio') {
            steps {
                git 'https://github.com/psf/requests.git'
            }
        }

        stage('Crear entorno virtual') {
            steps {
                sh 'python3 -m venv $VENV_DIR'
                sh '. $VENV_DIR/bin/activate && pip install --upgrade pip'
            }
        }

        stage('Instalar dependencias') {
            steps {
                sh '. $VENV_DIR/bin/activate && pip install -r requirements.txt || true'
                sh '. $VENV_DIR/bin/activate && pip install -e .[socks] pytest pytest-cov flake8'
            }
        }

        stage('Lint y análisis de calidad') {
            steps {
                sh '. $VENV_DIR/bin/activate && flake8 requests'
            }
        }

        stage('Tests con cobertura') {
            steps {
                sh '. $VENV_DIR/bin/activate && pytest --cov=requests tests/'
            }
        }

        stage('Empaquetado simulado') {
            steps {
                sh 'tar czf requests_package.tar.gz requests'
            }
        }

        stage('Despliegue simulado') {
            steps {
                sh 'mkdir -p staging'
                sh 'cp requests_package.tar.gz staging/'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/requests_package.tar.gz', fingerprint: true
        }
    }
}
