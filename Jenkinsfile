pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    environment {
        PATH = "${env.PATH}:/var/lib/jenkins/.local/bin"
    }
    stages {
        stage('Setup') {
            steps {
                sh '''
                    python3 -m pip install --upgrade pip pytest
                    if [ -f requirements.txt ]; then
                        python3 -m pip install -r requirements.txt
                    fi
                '''
            }
        }
        stage('Build') {
            steps {
                sh 'python3 -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            steps {
                sh 'mkdir -p test-reports'
                sh 'pytest --junitxml=test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') { 
            steps {
                sh "python3 -m pip install pyinstaller"
                sh "pyinstaller --onefile sources/add2vals.py"
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals*'
                }
            }
        }
    }
}
