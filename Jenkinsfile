 pipeline {
    agent any 
    stages {
        stage('Test') { 
            steps {
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
                        echo "'$container' does not exist." 2> /dev/null;
                    else 
                        docker stop $container;	
                        docker container prune -f;
                    fi

                    echo "Running pipline1_project..."
                    if  docker run -d --name pipline1_project --rm -p 9000:8080 pipline1_project;then
                        echo "Deployed on http://localhost:9000";
                    else
                        echo "Error in deploying pipline1_project";
                    fi
                ''' 
            }
        }
        stage('Monitor') { 
            steps {
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
}