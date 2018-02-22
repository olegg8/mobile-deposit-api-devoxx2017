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


stage ('Build') {

  // Asking for an agent with label 'docker-cloud'
  node {

    checkout scm

    // Let's retrieve the SHA-1 on the last commit (to identify the version we build)
    sh('git rev-parse HEAD > GIT_COMMIT')
    git_commit=readFile('GIT_COMMIT')
    short_commit=git_commit.take(7)

    // Let's build the application inside a Docker container
    docker.image('kmadel/maven:3.3.3-jdk-8').inside('-v /data:/data') {
        sh "mvn -DGIT_COMMIT='${short_commit}' -DBUILD_NUMBER=${env.BUILD_NUMBER} -DBUILD_URL=${env.BUILD_URL} clean package"
    }

    // Let's stash various files, mandatory of the pipeline
    //stash name: 'pom', includes: 'pom.xml'
    //stash name: 'jar-dockerfile', includes: '**/target/*.jar,**/target/Dockerfile'
    //stash name: 'deployment.yml', includes:'deployment.yml'
  }
}
