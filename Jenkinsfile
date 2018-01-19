pipeline {
    agent any

    environment {
        sel_hub = null
    }

    stages {
        stage("Prepare Selenium") {
            steps {
                echo "Umgebungsvariable TESTVAR: ${TESTVAR}"
                script {
                    sel_hub = docker.image('selenium/standalone-chrome:3.8.1-erbium').run('-p 4444:4444 -p -v /dev/shm:/dev/shm --name selenium-hub')
                }
            }
        }
        stage("Build") {
            agent {
                docker {
                    reuseNode true
                    image 'maven:3.5.2-jdk-8-alpine'
                }
            }
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }
        stage("Chrome Test") {
            agent {
                docker {
                    reuseNode true
                    image 'maven:3.5.2-jdk-8-alpine'
                }
            }
            steps {
                sh 'mvn -B test -Dselenium.browser=chrome -Dsurefire.rerunFailingTestsCount=5 -DHUB_PORT_4444_TCP_ADDR=wl-integration-test01.test-server.ag -DHUB_PORT=4444 -Dsleep=0'
            }
        }
    }

    post {
        always {
            script {
                sel_hub.stop()
            }
        }
    }
}