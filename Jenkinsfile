pipeline {
    agent any

    stages {
        stage('Environment Selection') {
            steps {
                input message: 'Check the environment(s) to deploy:',
                      ok: 'Yes',
                      parameters: [booleanParam(name: 'ubuntu_vm', defaultValue: false),
                                   booleanParam(name: 'mac_host', defaultValue: false)]
            }
        }

        stage('Deploy (Ubuntu VM)') {
            steps {
                script {
                    if (params.ubuntu_vm) {
                        // Start an SSH agent and run Docker commands on the server
                        sshagent(credentials: ['ssh-private-key-jenkins-container']) {
                            def sshCommand = """
                                cd /home/dzhai/Documents/grafana &&
                                git pull --rebase &&
                                docker-compose -f /home/dzhai/Documents/grafana/docker-compose.yml down &&
                                docker-compose -f /home/dzhai/Documents/grafana/docker-compose.yml up -d
                            """

                            // Execute the SSH command
                            def sshResult = sh(script: "ssh dzhai@192.168.0.12 -p 2212 '${sshCommand}'", returnStatus: true)

                            // Check the result of the SSH command
                            if (sshResult == 0) {
                                echo "SSH command executed successfully"
                            } else {
                                error "Error executing SSH command. Exit code: ${sshResult}"
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy (Mac Host)') {
            steps {
                script {
                    if (params.mac_host) {
                        // Start an SSH agent and run Docker commands on the server
                        sshagent(credentials: ['ssh-private-key-ubuntu-vm']) {
                            def sshCommand = """
                                cd /Users/dzhai/Documents/grafana &&
                                git pull --rebase &&
                                docker-compose -f /Users/dzhai/Documents/grafana/docker-compose-no-grafana.yml down &&
                                docker-compose -f /Users/dzhai/Documents/grafana/docker-compose-no-grafana.yml up -d
                            """

                            // Execute the SSH command
                            def sshResult = sh(script: "ssh dzhai@192.168.0.12 '${sshCommand}'", returnStatus: true)

                            // Check the result of the SSH command
                            if (sshResult == 0) {
                                echo "SSH command executed successfully"
                            } else {
                                error "Error executing SSH command. Exit code: ${sshResult}"
                            }
                        }
                    }
                }
            }
        }
    }
}
