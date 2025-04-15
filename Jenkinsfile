pipeline {
    agent any

    options {
        // Delete the workspace before starting the build
        skipStagesAfterUnstable()
        disableConcurrentBuilds()
    }
    // added a simple comment to test the pipeline

    stages {
        stage('Checkout') {
            steps {
                // Clone the Git repository
                git url: 'https://github.com/eslamsadawi/mydepiproject.git', branch: 'master'
            }
        }

        stage('Transfer Code to Ansible Server') {
            steps {
                // Create a directory for deployment files and copy only necessary files
                sh '''
                    # Create a clean directory for packaging
                    mkdir -p deploy_package
                    
                    # Copy all necessary files/directories (excluding Jenkins-specific files)
                    cp -r about.html contact.html design.png Dockerfile index.html shop-single.html shop.html README.md Ansible K8S assets deploy_package/
                    
                    # Create the tar file from the clean directory
                    cd deploy_package && tar -czf ../mydepiproject.tar.gz .
                '''
                
                // Transfer the tar file to the Ansible server
                sshagent(credentials: ['ansible-ssh-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} "mkdir -p ${REMOTE_PROJECT_DIR}"
                        scp -o StrictHostKeyChecking=no mydepiproject.tar.gz ubuntu@\${ANSIBLE_SERVER}:${REMOTE_PROJECT_DIR}/
                        ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} "cd ${REMOTE_PROJECT_DIR} && tar -xzf mydepiproject.tar.gz && rm mydepiproject.tar.gz"
                    """
                }
                
                // Clean up the local deployment package
                sh 'rm -rf deploy_package mydepiproject.tar.gz'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Running an Ansible playbook to create the Docker image via SSH
                sshagent(credentials: ['ansible-ssh-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} \\
                        "cd ${REMOTE_PROJECT_DIR} && ansible-playbook -i Ansible/inventory Ansible/create-image-cafe-app.yml"
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

        stage('Check Kubernetes Connectivity') {
            steps {
                // Check if Kubernetes cluster is accessible
                sshagent(credentials: ['ansible-ssh-credentials']) {
                    script {
                        try {
                            sh """
                                ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} \\
                                "kubectl cluster-info"
                            """
                            echo "Kubernetes cluster is accessible"
                        } catch (Exception e) {
                            echo "WARNING: Kubernetes cluster connectivity issue detected"
                            echo "Error message: ${e.getMessage()}"
                            echo "Continuing with deployment using --validate=false flag"
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Running an Ansible playbook to deploy the app
                sshagent(credentials: ['ansible-ssh-credentials']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@\${ANSIBLE_SERVER} \\
                        "cd ${REMOTE_PROJECT_DIR} && ansible-playbook -i Ansible/inventory Ansible/k8s-deploy.yml"
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

