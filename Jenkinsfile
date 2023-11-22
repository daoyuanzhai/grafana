pipeline {
    agent any

    stages {
        stage('Pull and Deploy (Mac)') {
            steps {
                script {
                    // Start an SSH agent and run Docker commands on the server
                    sshagent(credentials: ['ssh-mac-home-server']) {
                        def sshCommand = """
                            cd ~/Documents/grafana
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