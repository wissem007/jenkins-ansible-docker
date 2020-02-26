node {
   def registryProjet='registry.gitlab.com/xavki/presentations-jenkins/wartest'
   def IMAGE="${registryProjet}:version-${env.BUILD_ID}"
    stage('Build - Clone') {
          git 'https://github.com/priximmo/war-build-docker.git'
    }
    stage('Build - Maven package'){
            sh 'mvn package'
    }
    def img = stage('Build') {
          docker.build("$IMAGE",  '.')
    }
    stage('Build - Test') {
            img.withRun("--name run-$BUILD_ID -p 8081:8080") { c ->
            sh 'docker ps'
            sh 'netstat -ntaup'
            sh 'sleep 30s'
            sh 'curl 127.0.0.1:8081'
            sh 'docker ps'
          }
    }
    stage('Build - Push') {
          docker.withRegistry('https://registry.gitlab.com', 'reg1') {
              img.push 'latest'
              img.push()
          }
    }
    stage('Deploy - Clone') {
          git 'https://github.com/priximmo/jenkins-ansible-docker.git'
    }
    stage('Deploy - End') {
      ansiblePlaybook (
          colorized: true,
          become: true,
          playbook: 'playbook.yml',
         inventory: '${HOST},',
          extras: "--extra-vars 'image=$IMAGE'"
      )
    }

}

