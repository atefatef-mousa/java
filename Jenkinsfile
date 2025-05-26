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

        stage("Debug Java Setup") {
        steps {
            sh 'echo "JAVA_HOME: $JAVA_HOME"'
            sh 'echo "PATH: $PATH"'
            sh 'java -version'
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

}