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
     stage('Run Java Unit Tests') {
        withMaven(maven: 'M3') {
            /// Run the maven build
                sh "mvn -Dmaven.test.failure.ignore=true -Dserver.port=8090 -DskipTests clean package"
        }
    }



    stage('Final') {
        sh "echo done"
        sleep 5;
        sh "cat ${env.GIT_REPO_NAME}.yaml";
    }

    stage('Docker Build') {
            app = docker.build("onekoech/${env.GIT_REPO_NAME}")
    }
    stage('Docker Push') {
        docker.withRegistry('https://957907570595.dkr.ecr.us-east-2.amazonaws.com/', "ecr:us-east-2:awsecr-uat") {
            env.BUILDVERSION = BUILDVERSION()
            app.push("uat-${env.SHORT_COMMIT}-${env.BUILDVERSION}")
            app.push("latest")
        }

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

