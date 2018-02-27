  podTemplate(label: 'test', containers: [
    containerTemplate(name: 'jnlp', image: 'jenkins/jnlp-slave:latest', args: '${computer.jnlpmac} ${computer.name}'),
    containerTemplate(name: 'maven', image: 'maven:3.5.0-jdk-8-alpine', ttyEnabled: true, command: 'cat')
    ],
    volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
  ]) {

    node('test') {

      stage('Checkout') {
      def myRepo = git branch: 'master',
          credentialsId: '3d11bf3a-974e-46e9-9bf9-872734a65798',
          url: 'git@github.com:AlexandrSemak/mobile-deposit-api-devoxx2017.git'
      //def myRepo = checkout scm
      def gitCommit = myRepo.GIT_COMMIT
      def gitBranch = myRepo.GIT_BRANCH
      def shortGitCommit = "${gitCommit[0..10]}"
      def previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)
      }
      //stage('Checkout') {
        //git branch: 'master',
          //  credentialsId: '3d11bf3a-974e-46e9-9bf9-872734a65798',
            //url: 'git@github.com:AlexandrSemak/mobile-deposit-api-devoxx2017.git'
            //sh('git rev-parse HEAD > GIT_COMMIT')
            //git_commit=readFile('GIT_COMMIT')
            //short_commit=git_commit.take(7)
          //}

      stage('Package') {
        try {
          container('maven') {
            sh "mvn -DGIT_COMMIT='${shortGitCommit}' -DBUILD_NUMBER=${env.BUILD_NUMBER} -DBUILD_URL=${env.BUILD_URL} clean package"
          }
        } finally {
          //archiveArtifacts allowEmptyArchive: true, artifacts: '**/target/*.jar,**/target/Dockerfile'
          stash name: 'pom', includes: 'pom.xml'
          stash name: 'jar-dockerfile', includes: '**/target/*.jar,**/target/Dockerfile'
          stash name: 'deployment.yml', includes:'deployment.yml'
          stage('Build'){
            //unstash 'jar-dockerfile'
            docker.build "mobile-deposit-api:${dockerTag}"
          }
        }
      }

      //stage('Build Docker Image') {
      //unstash Spring Boot JAR and Dockerfile
    //  container('maven') {
      //    unstash 'jar-dockerfile'
        //  docker.build "mobile-deposit-api:${dockerTag}"
      //}
  }
}
