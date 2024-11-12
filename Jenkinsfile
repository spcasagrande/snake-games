pipeline {
    agent none
    stages {
        stage('CLONE GIT REPOSITORY') {
            agent {
                label 'ubuntu-appserver-CWEB2140'
            }
            steps {
                checkout scm
            }
        }  
 
        stage('SCA-SAST-SNYK-TEST') {
            agent any
            steps {
                script {
                    snykSecurity(
                        snykInstallation: 'Snyk',
                        snykTokenId: 'snyk_api_token',
                        severity: 'critical'
                    )
                }
            }
        }
 
        stage('SonarQube Analysis') {
            agent {
                label 'ubuntu-appserver-CWEB2140'
            }
            steps {
                script {
                    def scannerHome = tool 'SonarQube'
                    withSonarQubeEnv('SonarQube') {
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=gameapp \
                            -Dsonar.sources=."
                    }
                }
            }
        }
 
        stage('BUILD-AND-TAG') {
            agent {
                label 'ubuntu-appserver-CWEB2140'
            }
            steps {
                script {
                    def app = docker.build("spcasagrande/snake_games")
                    app.tag("latest")
                }
            }
        }
 
        stage('POST-TO-DOCKERHUB') {    
            agent {
                label 'ubuntu-appserver-CWEB2140'
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials') {
                        def app = docker.image("spcasagrande/snake_games")
                        app.push("latest")
                    }
                }
            }
        }
 
        stage('DEPLOYMENT') {    
            agent {
                label 'ubuntu-appserver-CWEB2140'
            }
            steps {
                sh "docker-compose down"
                sh "docker-compose up -d"   
            }
        }
    }
}
