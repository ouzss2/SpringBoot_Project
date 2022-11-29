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

         stage('Notifying') {
            steps {
             mail bcc: 'hehe', body: 'Validatio <project started a build you may check it ', cc: 'hehe', from: '', replyTo: '', subject: 'build is triggred', to: 'gmail@gmail.com'
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
                    docker login -u "username" -p "password"
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
                docker login -u "username" -p "password"
                    docker pull ouzss/springbootimage:latest  
                    docker stop springbootimage
                    docker rm springbootimage
                    docker run -v /opt/logs:/logs -p 9012:9012 --name springbootimage --restart=always -t -d ouzss/springbootimage:latest 

                '''
            }
        }

    }
  }
