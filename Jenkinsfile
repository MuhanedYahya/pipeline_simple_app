 pipeline {
    agent any 
    stages {
        stage('Test') { 
            steps {
                sh '''#!/bin/bash
                    cd pipline1_simple_app;
                    echo "installing jest framework...";
                    if npm install --save-dev jest;then
                        echo "running jest testing...";
                        npm test;
                        
                    fi
                ''' 
            }
        }
        stage('Build') { 
            steps {
                sh '''#!/bin/bash
                    echo "building docker image...";
                    if docker build . -t pipline1_project;then
                        echo "image successfully created";
                    fi
                ''' 
            }
        }
        stage('Deploy') { 
            steps {
                sh '''#!/bin/bash
                    container=pipline1_project;
                    running=$( docker container inspect -f '{{.State.Running}}' $container 2>/dev/null);

                    if [ $running -eq 1 ]; then
                        echo "'$container' does not exist.";
                    else 
                        docker stop $container;	
                    fi

                    echo "Running container..."
                    if  docker run -d --name pipline1_project --rm -p 9000:8080 pipline1_project;then
                        echo "Deployed on http://localhost:9000";
                    else
                        echo "Error in deploying container";
                    fi
                ''' 
            }
        }
    }
}