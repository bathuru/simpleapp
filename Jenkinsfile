def   VER_NUM;
def   REL_NUM;
def   mavenHome;
node{

    stage('Init') {
        VER_NUM = "1.0.${BUILD_NUMBER}";
        REL_NUM = "1.0.${BUILD_NUMBER}-RELEASE";
        mavenHome =  tool name: "Maven", type: "maven"
    }

    stage('Git Checkout') {
          git url: 'https://github.com/bathurugithub/simpleapp.git', branch: 'master'
    }

    stage(" Maven Build") {
          sh "${mavenHome}/bin/mvn clean versions:set -Dver=${VER_NUM} package "
    }

    stage('SonarQube Analysis') {
         withSonarQubeEnv('SonarQubeServer') {
                 sh "${mavenHome}/bin/mvn sonar:sonar"
          }
     }

    stage('Copy to Nexus Repo'){
                    nexusPublisher  nexusInstanceId: 'NexusRepoServer',
                   nexusRepositoryId: 'DevopsRepo',
                            packages: [[$class: 'MavenPackage',
                      mavenAssetList: [[classifier: '', extension: '', filePath: "${WORKSPACE}/target/simpleapp-${REL_NUM}.war"]],
                     mavenCoordinate: [artifactId: 'simpleapp', groupId: 'com.apple', packaging: 'war', version: "${REL_NUM}"]]]
   }

  /* stage('Build & Push Docker Image'){
           sh 'docker build -t bathurudocker/simpleapp:${VER_NUM} .'
           withCredentials([string(credentialsId: 'dockerHubPwd', variable: 'dockerpwd')]) {
                  sh "docker login -u bathurudocker -p ${dockerpwd}"
           }
           sh 'docker push bathurudocker/simpleapp:${VER_NUM}'
           sh 'docker rmi bathurudocker/simpleapp:${VER_NUM}'
   }*/

   /*stage('Deploy Into PROD') {
           sshagent(['docker_Server_SSH']) {
               sh "ssh -o StrictHostKeyChecking=no ec2-user@52.66.240.70  sudo docker rm -f simpleapp || true"
               sh "ssh -o StrictHostKeyChecking=no ec2-user@52.66.240.70  sudo docker run  -d -p 8010:8080 --name simpleapp bathurudocker/simpleapp:${VER_NUM}"
          }
     }*/
}
