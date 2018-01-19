pipeline {
    agent any
    stages {
        stage("Prepare Selenium") {
            agent {
                docker {
                    reuseNode true
                    image 'selenium/standalone-chrome'
                    args  '-p 4444:4444 -v /dev/shm:/dev/shm'
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
}