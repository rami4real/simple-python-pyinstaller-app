pipeline {
    agent none

    environment {
        DOCKER_HOST = 'tcp://localhost:2375'
    }

    stages {
        stage('Build') {
            agent {
                docker.image('myjenkins-blueocean:2.426.1-1').withRun('-v /var/run/docker.sock:/var/run/docker.sock') {
                    // Your build steps here
                }
            }
            steps {
                script {
                    docker.image('python:3.12.0-alpine3.18').inside {
                        sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                        stash(name: 'compiled-results', includes: 'sources/*.py*')
                    }
                }
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
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
    }
}
