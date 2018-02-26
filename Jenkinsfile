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

def label = UUID.randomUUID().toString()

  podTemplate(label: 'test', containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:latest', args: '${computer.jnlpmac} ${computer.name}'),
    containerTemplate(name: 'maven', image: 'maven:3.5.0-jdk-8-alpine', ttyEnabled: true, command: 'cat')
    ]) {

    node('test') {
      stage('Checkout') {
        git 'https://github.com/AlexandrSemak/mobile-deposit-api-devoxx2017.git'
      }
      stage('Package') {
        try {
          container('maven') {
            sh 'mvn -B clean install -Dmaven.test.skip=true -Dfindbugs.fork=false'
          }
        } finally {
          archiveArtifacts allowEmptyArchive: true, artifacts: '**/target/*.hpi,**/target/*.jpi'
        }
      }
    }
  }
