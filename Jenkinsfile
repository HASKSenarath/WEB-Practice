pipeline {
    agent any
   
    stages {
        stage('Create web directory')
        {
            input {
              message 'Enter the data'
              parameters {
                    string(name:'AUTHOR', defaultValue: 'Sapumal', description: 'Author of the web application deployment ')
                    string(name:'ENVIRONMENT', defaultValue: 'Development',description: 'Environment to deploy')
                 }
            }
            steps{
                echo "The responsible of this project is ${AUTHOR} and and will be deployed in ${ENVIRONMENT}"
                //First, drop the directory if exists
                sh 'rm -rf /home/jenkins/web'
                //Create the directory
                sh 'mkdir /home/jenkins/web'
            }
        }
        stage('Drop the Apache HTTPD Docker container'){
            steps {
                echo 'droping the container...'
                sh 'docker rm -f apache1 || true'
            }
        }
        stage('Create the Apache httpd container') {
            steps {
                echo 'Creating the container...'
                // Attach container to the same "jenkins" network as jenkins-master
                sh 'docker run -dit --name apache1 --network jenkins -p 9000:80 -v /home/jenkins/web:/usr/local/apache2/htdocs/ httpd'
            }
        }
        stage('Copy the web application to the container directory') {
            steps {
                echo 'Copying web application...'
                sh 'cp -r web/* /home/jenkins/web'
            }
        }
        stage('Checking the app') {
            steps {
                echo 'Testing the web app'
                // Call the container by name from within the Jenkins container
                sh 'wget http://apache1'
            }
        }       
    }
}
