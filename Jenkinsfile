node('ubuntu-appserver-CWEB2140')
{
 
def app
stage('Cloning Git')
{
    /* Let's make sure we have the repository cloned to our workspace */
    checkout scm
}

stage('Snyk-SAST-SCA-Test')
{
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

stage('SonarQube Analysis') 
{
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
 
stage('Build-and-Tag')
{
    /* This builds the actual image;
         * This is synonymous to docker build on the command line */
    app = docker.build('spcasagrande/snake_games')
}
 
stage('Post-to-dockerhub')
{
    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub_credentials')
    {
        app.push('latest')
    }
   
}
