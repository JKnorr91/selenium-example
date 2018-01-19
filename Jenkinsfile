pipeline {
    agent any

    environment {
        sel_hub = null
        sel_chrome = null
    }

    stages {
        stage("Prepare Selenium") {
            steps {
                script {
                    sel_hub = docker.image('selenium/hub:3.4.0').run('-p 4444:4444 --name selenium-hub')
                    sel_chrome = docker.image('selenium/node-chrome-debug:3.4.0').run('-p 5901:5900 --link selenium-hub:hub')
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
                sel_chrome.stop()
            }
        }
    }
}