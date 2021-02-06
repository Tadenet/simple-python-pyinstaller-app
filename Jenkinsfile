pipeline {
    agent any
    stages {
        stage('Build') { 
            agent {
                docker {
                    image 'python:2-alpine' 
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
                stash(name: 'compiled-results', includes: 'sources/*.py*') 
            }
        }
	stage('Test') {
		agent {
			docker {
				image 'qnib/pytest'
			}
		}
		steps {
			sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
			sh 'coverage run --source=./sources sources/test_calc.py'
			sh 'coverage xml -o test-reports/coverage.xml'
		}
		post {
			always {
				junit 'test-reports/results.xml'
			}
		}
	}
	stage('SonarQube') {
                steps {
                script {
                 scannerHome = tool 'sonarqube1';
                }
                withSonarQubeEnv('SonarQubeScanner1') {
                        sh "${scannerHome}/bin/sonar-scanner"
                }
                }
        }
    }
}
