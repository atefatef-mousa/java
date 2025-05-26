@Library('global_lib_x')_

pipeline{
    agent {
        label 'agent_1'
    }

    tools{
        jdk "java-8"
        maven "maven-3"
    }

    environment{
        DOCKER_USER = credentials('dockerhub-user')
        DOCKER_PASS = credentials('dockerhub-password')
    }

    parameters {
        string defaultValue: '${BUILD_NUMBER}', description: 'Enter the version of the docker image', name: 'VERSION'
        choice choices: ['true', 'false'], description: 'Skip test', name: 'TEST'
    }

    stages{
        stage("VM info"){
            steps{
                script{
                    def VM_IP = vmIp()
                    sh "echo ${VM_IP}"
                }
            }
        }
        stage('Install Maven') {
            steps {
                script {
                    // Install Maven if not already installed on the agent
                    sh '''
                        if ! command -v mvn &> /dev/null; then
                            echo "Installing Maven..."
                            wget https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz
                            tar -xzf apache-maven-3.9.6-bin.tar.gz -C /opt/
                            ln -s /opt/apache-maven-3.9.6 /opt/maven
                            export PATH="/opt/maven/bin:$PATH"
                        else
                            echo "Maven is already installed."
                        fi
                    '''
                }
            }
        }
        stage("Build java app"){
            steps{
                script{
                    sayHello "ITI"
                }
                sh "mvn clean package install -Dmaven.test.skip=${TEST}"
            }
        }
        stage("build java app image"){
            steps{
                script{
                    def dockerx = new org.iti.docker()
                    dockerx.build("java", "${VERSION}")
                }
                sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS} "
            }
        }
        stage("push java app image"){
            steps{
                script{
                    def dockerx = new org.iti.docker()
                    dockerx.login("${DOCKER_USER}", "${DOCKER_PASS}")
                    dockerx.push("${DOCKER_USER}", "${DOCKER_PASS}")
                }
            }
        }
    }

    post{
        always{
            sh "echo 'Clean the Workspace'"
            cleanWs()
        }
        failure {
            sh "echo 'failed'"
        }
    }
}