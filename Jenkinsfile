 pipeline {
    agent any 
    stages {
        stage('Test') { 
            steps {
                script {
                    last_started = env.STAGE_NAME
                }
                sh '''#!/bin/bash
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
                script {
                    last_started = env.STAGE_NAME
                }
                // sh '''#!/bin/bash
                //     echo "building docker image...";
                //     if docker build . -t pipline1_project;then
                //         echo "image successfully created";
                //     fi
                // '''
                sh '''#!/bin/bash
                    echo "building docker image...";
                    if docker build . -t muhanedyahya/pipline-v1-app;then
                        echo "image successfully created.";
                        echo "pushing image to docker hub.....";
                        if docker login -u muhanedyahya -p 'Myahya123!root';then
                            if docker push muhanedyahya/pipline-v1-app;then
                                echo "image pushed seccessfully.";
                            else
                                echo "error in pushing image!!! something went wrong";
                            fi
                        else 
                            echo "cant login to docker hub!!!";
                        fi
                    fi
                '''  
            }
        }
        stage('Deploy on aws ec2') { 
            steps {
                script {
                    last_started = env.STAGE_NAME
                }
                // sh '''#!/bin/bash
                //     container=pipline1_project;
                //     running=$( docker container inspect -f '{{.State.Running}}' $container 2>/dev/null);

                //     if [ $running -eq 1 ]; then
                //         echo "'$container' does not exist." 2> /dev/null;
                //     else 
                //         docker stop $container;	
                //         docker container prune -f;
                //     fi

                //     echo "Running pipline1_project..."
                //     if  docker run -d --name pipline1_project --rm -p 9000:8080 pipline1_project;then
                //         echo "Deployed on http://localhost:9000";
                //     else
                //         echo "Error in deploying pipline1_project";
                //     fi
                // '''
                sshagent(credentials : ['44.203.124.0']) {
                    sh '''
                        ssh -tt ec2-user@44.203.124.0 -o StrictHostKeyChecking=no "sudo docker pull muhanedyahya/pipline-v1-app:latest && docker stop pipline-app && docker container prune -f && sudo docker run --name pipline-app -d --rm -p 80:8080 muhanedyahya/pipline-v1-app"
                    '''
                }
                
            }
        }
        stage('Monitor') { 
            steps {
                script {
                    last_started = env.STAGE_NAME
                }
                sh '''#!/bin/bash
                    container=prometheus;
                    running=$( docker container inspect -f '{{.State.Running}}' $container 2>/dev/null);

                    if [ $running -eq 1 ]; then
                        echo "There is no prometheus, we are preparing it now..";
                        echo "'$container' does not exist." 2> /dev/null ;
                    else 
                        echo "Prometheus is already running we will make a simple refresh";
                        docker stop $container;
                        docker container prune -f;
                    fi

                    echo "Running prometheus..."
                    if  docker run --name prometheus -p 9090:9090 -d -v $(pwd)/prometheus.yml:/opt/bitnami/prometheus/conf/prometheus.yml  bitnami/prometheus:latest;then
                        echo "prometheus deployed on http://localhost:9090";
                        echo "...................................";
                            container=grafana;
                            running=$( docker container inspect -f '{{.State.Running}}' $container 2>/dev/null);

                            if [ $running -eq 1 ]; then
                                echo "'$container' does not exist." 2> /dev/null ;
                                echo "There is no prometheus, we are preparing it now..";
                            else 
                                echo "Grafana is already running we will make a simple refresh";
                                docker stop $container;
                                docker container prune -f;
                            fi

                            echo "Running Grafana..."
                            if  docker run -d --name=grafana -p 3000:3000 grafana/grafana;then
                                echo "Grafana deployed on http://localhost:3000";
                                echo "...................................";
                                echo "Your can setup your your dashboard from the link";
                                echo "username:admin && password admin";
                                
                            else
                                echo "Error in deploying Grafana. check your monitor stage!";
                            fi
                    else
                        echo "Error in deploying prometheus also Grafana will not deployed. check your monitor stage!";
                    fi
                ''' 
            }
        }
    }

    post {  
         success {  
             mail(body: 'All stages of your project have been successfully prepared and deployed.', subject: 'Project successfully deployed!', to: 'yahya.muhaned@gmail.com')
         }  
         failure {  
            mail(body: "An error occurred during the $last_started stage", subject: "$last_started stage Alert !!", to: 'yahya.muhaned@gmail.com') 
         }  

     }  
}