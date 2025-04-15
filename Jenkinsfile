pipeline {
    agent any

    options {
        // Delete the workspace before starting the build
        skipStagesAfterUnstable()
        disableConcurrentBuilds()
    }

    environment {
        ANSIBLE_SERVER = 'ansible-server' // SSH server for ansible deployment
        REMOTE_PROJECT_DIR = '/opt/deploy/mydepiproject' // Directory on remote server where code will be stored
    }

    // triggers {
    //     // Poll SCM every minute (you might want to change this to a more reasonable schedule)
    //     pollSCM('* * * * *')
    //     // GitHub hook trigger for GIT SCM polling
    //     githubPush()
    // }
    // testing pipeline trgger 
    // testing pipeline trgger 
    // testing pipeline trgger 
    // testing pipeline trgger 
   
    stages {
        stage('Checkout') {
            steps {
                // Clone the Git repository
                git url: 'https://github.com/eslamsadawi/mydepiproject.git', branch: 'master'
            }
        }

        stage('Transfer Code to Ansible Server') {
            steps {
                // Create a tar file of the project
                sh 'tar -czf mydepiproject.tar.gz .'
                
                // Transfer the tar file to the Ansible server
                sshagent(credentials: ['ansible-ssh-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} "mkdir -p ${REMOTE_PROJECT_DIR}"
                        scp -o StrictHostKeyChecking=no mydepiproject.tar.gz ubuntu@\${ANSIBLE_SERVER}:${REMOTE_PROJECT_DIR}/
                        ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} "cd ${REMOTE_PROJECT_DIR} && tar -xzf mydepiproject.tar.gz && rm mydepiproject.tar.gz"
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                // Running an Ansible playbook to create the Docker image via SSH
                sshagent(credentials: ['ansible-ssh-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} \\
                        "cd ${REMOTE_PROJECT_DIR} && ansible-playbook Ansible/create-image-cafe-app.yml"
                    """
                }
            }
        }
        //retriger for the pipeline 
        stage('Test') {
            steps {
                // Example of a test stage (adjust based on your project)
                echo 'Running unit tests...'
                // Add your testing commands here
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Running an Ansible playbook to deploy the app
                sshagent(credentials: ['ansible-ssh-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} \\
                        "cd ${REMOTE_PROJECT_DIR} && ansible-playbook Ansible/k8s-deploy.yml"
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully!'
        }
        failure {
            echo 'CI/CD pipeline failed.'
        }
    }
}

