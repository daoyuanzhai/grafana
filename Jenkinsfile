pipeline {
    agent any

    stages {
        stage('Environment Selection') {
            steps {
                def env_selections = input(
                    message: 'Check the environment(s) to deploy:',
                    ok: 'Go',
                    parameters: [
                        booleanParam(name: 'ubuntu_vm', defaultValue: false),
                        booleanParam(name: 'mac_host', defaultValue: false)
                    ]
                )
                // Set the environment variable to pass to subsequent stages
                env.DEPLOY_UBUNTU_VM = params.ubuntu_vm
                env.DEPLOY_MAC_HOST = params.mac_host
            }
        }

        stage('Deploy (Ubuntu VM)') {
            when {
                expression { env.DEPLOY_UBUNTU_VM == 'true' }
            }
            steps {
                script {
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

        stage('Deploy (Mac Host)') {
            when {
                expression { env.DEPLOY_MAC_HOST == 'true' }
            }
            steps {
                script {
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
