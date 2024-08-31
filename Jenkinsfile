pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    try {
                        checkout scm
                        sh 'docker image build -t tetris:latest .'
                        echo 'Build stage completed successfully'
                    } catch (Exception e) {
                        echo 'Build stage failure'
                        error 'Build failed'
                    }
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    try {
                        sh 'docker build -t tests -f DockerfileTest .'
                        sh 'docker run tests > test-results.txt'
                        def testResults = readFile('test-results.txt')
                        echo testResults
                        if (testResults.contains('All tests passed')) {
                            echo 'All tests passed'
                        } else {
                            echo 'Some tests failed'
                            error 'Test stage failed'
                        }
                    } catch (Exception e) {
                        echo 'Testing stage failure'
                        error 'Test failed'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    try {
                        sh 'docker run -d -p 3000:3000 --name tetris tetris:latest'
                        echo 'Deploy stage completed successfully'
                    } catch (Exception e) {
                        echo 'Deploy stage failure'
                        error 'Deploy failed'
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed'
        }
    }
}
