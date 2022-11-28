#!/usr/bin/env groovy

pipeline {

    agent any

    options { disableConcurrentBuilds() }


    stages {

         stage('Permissions') {
            steps {
                sh 'chmod 775 *'
            }
        }

         stage('Email') {
            steps {
                        emailext body: 'a build has been started', recipientProviders: [developers()], subject: 'build triggred', to: 'bochsamia@gmail.com'
            }
        }
        


        stage('Compile stage') {
            steps {
                sh "mvn clean compile"
            }
           }


        stage('install') {
             steps {
                sh "mvn install -Dmaven.test.skip=true -P prod"
                }
            }

        stage('Update Docker UAT image') {

            steps {
                sh '''
                    docker login -u "ouzss" -p "Samia@58623712"
                    docker build --no-cache -t springbootimage:latest .
                    docker tag springbootimage:latest ouzss/springbootimage:latest
                    docker push ouzss/springbootimage:latest
                    docker rmi springbootimage:latest
                '''
                    }
            }

        stage('Update UAT container') {

            steps {
                sh '''
                docker login -u "ouzss" -p "Samia@58623712"
                    docker pull ouzss/springbootimage:latest  
                    docker stop springbootimage
                    docker rm springbootimage
                    docker run -v /opt/logs:/logs -p 9012:9012 --name springbootimage --restart=always -t -d ouzss/springbootimage:latest 

                '''
            }
        }

    }
  }
