pipeline {
    agent any

    triggers {
        pollSCM("H/5 * * * *")
    }

    stages {
        stage("Verify tooling") {
            steps {
                sh "/usr/bin/docker version"
                sh "/usr/bin/docker info"
            }
        }
        stage("Build") {
            steps {
                git branch: "master", url: "https://github.com/basialx/mimstris.git"

                script {
                    def dockerBuildOutput = sh(script: "/usr/bin/docker build -t basialx/tetris:latest .", returnStatus: true)
                    if (dockerBuildOutput == 0) {
                        currentBuild.result = "SUCCESS"
                    } else {
                        currentBuild.result = "FAILURE"
                    }
                }
            }
            post {
                success {
                    echo "Build stage success"
                }
                failure {
                    echo "Build stage failure"
                }
            }
        }

        stage("Test") {
            steps {
                script {
                    def testResult = sh(script: "/usr/bin/docker build -t basialx/test -f DockerfileTest . && /usr/bin/docker run --rm basialx/test", returnStatus: true)
                    if (testResult == 0) {
                        currentBuild.result = "SUCCESS"
                    } else {
                        currentBuild.result = "FAILURE"
                    }
                }
            }
            post {
                always {
                    echo "Tests finished"
                }
                success {
                    echo "Testing stage success"
                }
                failure {
                    echo "Testing stage failure"
                }
            }
        }

        stage("Deploy") {
            steps {
                script {
                    def dockerRun = "/usr/bin/docker run --name app -d -p 3000:3000 basialx/tetris"
                    def dockerRunOutput = sh(script: dockerRun, returnStdout: true).trim()

                    if (dockerRunOutput) {
                        echo "Container run success: ${dockerRunOutput}"
                        currentBuild.result = "SUCCESS"
                    } else {
                        error "Container run failure"
                        currentBuild.result = "FAILURE"
                    }
                }
            }
            post {
                success {
                    echo "Deploy stage success"
                }
                failure {
                    echo "Deploy stage failure"
                }
            }
        }
    }
}
