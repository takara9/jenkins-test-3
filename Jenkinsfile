podTemplate(
  label: 'mypod',
  containers: [
    containerTemplate(name: 'dockerd', image: 'docker:dind', ttyEnabled: true, alwaysPullImage: true, privileged: true,
      command: 'dockerd --host=unix:///var/run/docker.sock --host=tcp://127.0.0.1:2375 --storage-driver=overlay')
  ],
  volumes: [
    emptyDirVolume(memory: false, mountPath: '/var/lib/docker')
  ]
) 
{
  node ('mypod') {
    withCredentials([
      usernamePassword(credentialsId: 'docker_id', usernameVariable: 'DOCKER_ID_USR', passwordVariable: 'DOCKER_ID_PSW')
    ]) {
      stage('pull') {
        container('dockerd') {
            git url: 'https://github.com/takara9/node-express-login'	
            stage 'pull'
            sh 'docker pull alpine'
	    stage 'view'
	    sh 'ls -lR'
        }
      }
      stage('confirm') {
        container('dockerd') {
            stage 'git'
            sh 'git -h'
            stage 'view'
            sh 'docker images'
            stage 'view2'
            sh 'docker ps'

        }
      }
    }
  }
}