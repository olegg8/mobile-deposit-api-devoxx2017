/**
 * Build and test the kubernetes plugin using the plugin itself in a Kubernetes cluster
 *
 * A `jenkins` service account needs to be created using src/main/kubernetes/service-account.yml
 *
 * A PersistentVolumeClaim needs to be created ahead of time with the definition in examples/maven-with-cache-pvc.yml
 *
 * NOTE that typically writable volumes can only be attached to one Pod at a time, so you can't execute
 * two concurrent jobs with this pipeline. Or change readOnly: true after the first run
 */

  podTemplate(label: 'test', containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:latest', args: '${computer.jnlpmac} ${computer.name}'),
    containerTemplate(name: 'maven', image: 'maven:3.5.0-jdk-8-alpine', ttyEnabled: true, command: 'cat')
    ],
    volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {

    node('test') {
      stage('Checkout') {
        git branch: 'master',
            credentialsId: '3d11bf3a-974e-46e9-9bf9-872734a65798',
            url: 'git@github.com:AlexandrSemak/mobile-deposit-api-devoxx2017.git'
          }

      stage('Package') {
        try {
          container('maven') {
            sh('git rev-parse HEAD > GIT_COMMIT')
            git_commit=readFile('GIT_COMMIT')
            short_commit=git_commit.take(7)
            sh "mvn -DGIT_COMMIT='${short_commit}' -DBUILD_NUMBER=${env.BUILD_NUMBER} -DBUILD_URL=${env.BUILD_URL} clean package"
          }
        } finally {
          //archiveArtifacts allowEmptyArchive: true, artifacts: '**/target/*.jar,**/target/Dockerfile'
          stash name: 'pom', includes: 'pom.xml'
          stash name: 'jar-dockerfile', includes: '**/target/*.jar,**/target/Dockerfile'
          stash name: 'deployment.yml', includes:'deployment.yml'
        }
      }

      stage('Build Docker Image') {
      //unstash Spring Boot JAR and Dockerfile
      dir('target') {
        unstash 'jar-dockerfile'
        docker.build "mobile-deposit-api:${dockerTag}"
      }
    }
  }
}
