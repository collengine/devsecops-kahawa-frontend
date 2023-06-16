try {
node() {
    def app
    stage('Clone Repository')
    {
        cleanWs()
        final scmVars = checkout(scm)
        env.BRANCH_NAME = scmVars.GIT_BRANCH
        env.SHORT_COMMIT = "${scmVars.GIT_COMMIT[0..7]}"
        env.GIT_REPO_NAME = scmVars.GIT_URL.replaceFirst(/^.*\/([^\/]+?).git$/, '$1')
    }

    stage('Docker Build') {
            app = docker.build("onekoech/${env.GIT_REPO_NAME}")
    }

    stage('Push image') {
        withDockerRegistry([ credentialsId: "docker-hub-creds", url: "" ]) {
        dockerImage.push()
        }
    } 



}

} catch(Error|Exception e) {
  //Finish failing the build after telling someone about it
  throw e
} finally {
    // Post build steps here
    /* Success or failure, always run post build steps */
    // send email
    // publish test results etc
}
def version()
{
    pom = readMavenPom file: 'pom.xml'
    return pom.version
}
def BUILDVERSION()
{
    timestamp=Calendar.getInstance().getTime().format('YYYYMMddHHmmss',TimeZone.getTimeZone('EAT'))
    return timestamp
}

