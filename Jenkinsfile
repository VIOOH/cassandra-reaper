properties([
    [$class: 'BuildDiscarderProperty', strategy: [
        $class: 'LogRotator', numToKeepStr: '10', artifactNumToKeepStr: '10']],
])



node{

  env.NODEJS_HOME = "${tool 'node'}"
  env.PATH="${env.NODEJS_HOME}/bin:${env.PATH}"  

  stage ('Build') {

    checkout scm

    withMaven(
        maven: 'Maven-3.5.0') {

      // Run the maven build
      sh """
          echo \$PATH
          which node
          npm install
          mvn clean install -Drat.numUnapprovedLicenses=10
          """
    }
  }

  stage("Package") {
    when {
        branch 'develop'
        branch 'master'
      }
      sh """
          cd src/server/
          sudo docker build src/main/docker -t cassandra-reaper:${env.BRANCH_NAME}-${env.BUILD_NUMBER}
          sudo docker tag cassandra-reaper:${env.BRANCH_NAME}-${env.BUILD_NUMBER} 517256697506.dkr.ecr.eu-west-1.amazonaws.com/apps/cassandra-reaper:${env.BRANCH_NAME}-${env.BUILD_NUMBER}
          DOCKER_LOGIN="sudo \$(aws ecr get-login --no-include-email --region=eu-west-1 --registry-ids 517256697506)"
          eval "\$DOCKER_LOGIN"
          sudo docker push 517256697506.dkr.ecr.eu-west-1.amazonaws.com/apps/cassandra-reaper:${env.BRANCH_NAME}-${env.BUILD_NUMBER}
      """
  }
}