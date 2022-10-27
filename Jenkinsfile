#!groovy?
 
pipeline {
    agent any
    options {
        buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '2'))
    }
    stages {
        stage ('Pull Request API') {
            when { 
                allOf {
                    // changeRequest target: 'main' 
                    changeset "api/*"
                }
            }
            agent {
                docker { image 'node:16' }
            }
            steps {
                dir('api') {
                    script {
                        sh 'npm i && npm run test'
                    }
                }
            }
        }
        stage('Pull Request Database') {
            when { 
                allOf {
                    // changeRequest target: 'main' 
                    changeset "database/*"
                }
            }
            steps {
                sh (script: 'docker-compose up --abort-on-container-exit')
            }
        }
        stage('Deploy Database') {
            steps {
                script {
                    docker.image('flyway/flyway').withRun('-v /database:/flyway/sql', 
                        '-url=jdbc:postgresql://db/test -schemas=public -user=postgres -password=password -connectRetries=5') { c ->
                        sh "docker exec ${c.id} ls flyway"
                        sh "docker logs --follow ${c.id}"
                    }
                }
                // apply database
                // docker.image('flyway/flyway').withRun {c ->
                //     sh '-url=jdbc:postgresql://db/test -schemas=public -user=postgres -password=password -connectRetries=5 migrate'
                // }
            }
        }

        stage('Deploy API') {
            steps {
                // apply api lightsail
                echo 'Deploy API'
            }
        }

        stage('Deploy UI') {
            steps {
                // apply UI S3
                echo 'Deploy UI'
            }
        }
    }
}
