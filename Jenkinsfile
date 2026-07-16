pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compile') {
            steps {
                sh '''
                    python3 -m venv $VENV_DIR
                    . $VENV_DIR/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    python -m py_compile src/myapp/*.py
                '''
            }
        }

        stage('Unit Test') {
            steps {
                sh '''
                    . $VENV_DIR/bin/activate
                    export PYTHONPATH=src
                    pytest tests/ --junitxml=result.xml
                '''
            }
            post {
                always {
                    junit 'result.xml'
                }
            }
        }

        stage('Package') {
            steps {
                sh '''
                    . $VENV_DIR/bin/activate
                    python setup.py sdist bdist_wheel
                '''
                archiveArtifacts artifacts: 'dist/*.whl, dist/*.tar.gz', fingerprint: true
            }
        }
    }
}
