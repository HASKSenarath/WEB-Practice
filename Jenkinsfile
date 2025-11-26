pipeline {
    agent any
   
    stages {
        stage('Create web directory') {
            input {
                message 'Enter the data'
                parameters {
                    string(
                        name: 'AUTHOR',
                        defaultValue: 'Sergio',
                        description: 'Author of the web application deployment '
                    )
                    string(
                        name: 'ENVIRONMENT',
                        defaultValue: 'Development',
                        description: 'Environment to deploy'
                    )
                }
            }
            steps {
                echo "The responsible of this project is ${AUTHOR} and and will be deployed in ${ENVIRONMENT}"
                // First, drop the directory if it exists
                sh 'rm -rf /home/jenkins/web'
                // Create the directory
                sh 'mkdir -p /home/jenkins/web'
            }
        }

        stage('Drop the Apache HTTPD Docker container') {
            steps {
                echo 'droping the container...'
                // Do not fail if the container does not exist
                sh 'docker rm -f apache1 || true'
            }
        }

        stage('Create the Apache httpd container') {
            steps {
                echo 'Creating the container...'
                // Start Apache in the same "jenkins" network, no volume mount here
                sh 'docker run -dit --name apache1 --network jenkins -p 9000:80 httpd'
            }
        }

        stage('Copy the web application to the container directory') {
            steps {
                echo 'Copying web application...'
                // Copy from workspace into a local directory inside the Jenkins container
                sh 'cp -r web/* /home/jenkins/web'
                // Now copy from Jenkins container into the Apache container
                sh 'docker cp /home/jenkins/web/. apache1:/usr/local/apache2/htdocs/'
            }
        }

        stage('Checking the app') {
            steps {
                echo 'Testing the web app'
                // From Jenkins container, call Apache by container name on port 80
                sh 'wget http://apache1'
            }
        }
    }
}
