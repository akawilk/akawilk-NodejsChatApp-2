node('Ubuntu-Appserver-3120')
{
 
def app
stage('Cloning Git')
{
    /* Let's make sure we have the repository cloned to our workspace */
    checkout scm
}


stage('SCA-SAST-SNYK-TEST')
{

    snykSecurity(
        snykInstallation:'Snyk',
        snykTokenId: 'synk_api',
        severity: 'critical'
    )

}

stage('Build-And-Tag-')
{
    /* This builds the actual image; 
         * This is synonymous to docker build on the command line */
    app = docker.build('akawilk90/nodejschatapp')
}
 
stage('Push-to-dockerhub')
{
    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-creds-2')
    {
        app.push('latest')
    }
}

stage('Pull-Image-Run-App') {
        // Find and stop any Docker container using port 80
        sh '''
        CONTAINER_ID=$(docker ps -q --filter "publish=80")
        if [ -n "$CONTAINER_ID" ]; then
            echo "Stopping container using port 80: $CONTAINER_ID"
            docker stop $CONTAINER_ID
            docker rm $CONTAINER_ID
        else
            echo "No container using port 80."
        fi
        '''
}
 
stage('Deploy')
{
    sh "docker-compose down"
    sh "docker-compose up -d"
}
 
}
