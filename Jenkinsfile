// Mandatory environment variables:
// DOCKER_REGISTRY (without http://, no ending /)
// k8s_username
// k8s_password
// k8s_tenant
// k8s_name
// k8s_resourceGroup

// You need to create 2 Jenkins Credentials
// * 1 type 'Secret file', with ID 'kuby'
// * 1 type 'username with password' with ID 'test-registry'

def buildVersion = null
def short_commit = null
echo "Building ${env.BRANCH_NAME}"


podTemplate(label: 'mypod', containers: [
    //containerTemplate(name: 'mvn', image: 'kmadel/maven:3.3.3-jdk-8', ttyEnabled: true, command: 'cat'),
    containerTemplate(name: 'maven', image: 'maven:3.5.2-jdk-8-alpine', ttyEnabled: true, command: 'cat')
    //containerTemplate(name: 'docker', image: 'docker', ttyEnabled: true, command: 'cat'),
    //containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.8.0', command: 'cat', ttyEnabled: true),
  ],
  volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {

  node('mypod') {
    stage('Checkout') {
        sh 'git --version'
        checkout scm
        sh 'git --version'
      }
    stage('Build'){
      // Let's retrieve the SHA-1 on the last commit (to identify the version we build)
      sh('git rev-parse HEAD > GIT_COMMIT')
      git_commit=readFile('GIT_COMMIT')
      short_commit=git_commit.take(7)

      container('maven'){
        sh "mvn -DGIT_COMMIT='${short_commit}' -DBUILD_NUMBER=${env.BUILD_NUMBER} -DBUILD_URL=${env.BUILD_URL} clean package"
    // Let's build the application inside a Docker container
    //docker.image('kmadel/maven:3.3.3-jdk-8').inside('-v /data:/data') {
      //  sh "mvn -DGIT_COMMIT='${short_commit}' -DBUILD_NUMBER=${env.BUILD_NUMBER} -DBUILD_URL=${env.BUILD_URL} clean package"
    }

    // Let's stash various files, mandatory of the pipeline
    stash name: 'pom', includes: 'pom.xml'
    stash name: 'jar-dockerfile', includes: '**/target/*.jar,**/target/Dockerfile'
    stash name: 'deployment.yml', includes:'deployment.yml'
  }
}

//def dockerTag = "${env.BUILD_NUMBER}-${short_commit}"

//stage('Version Release') {

  //container('docker'){

    // Extract the version number from the pom.xml file
    //unstash 'pom'
    //def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
    //if (matcher) {
      //  buildVersion = matcher[0][1]
        //echo "Release version: ${buildVersion}"
    //}
    //matcher = null

    //def mobileDepositApiImage
    //stage('Build Docker Image') {
      //unstash Spring Boot JAR and Dockerfile
      //dir('target') {
        //unstash 'jar-dockerfile'
        //mobileDepositApiImage = docker.build "mobile-deposit-api:${dockerTag}"
      //}
    //}
  //}
//}
//}
}
