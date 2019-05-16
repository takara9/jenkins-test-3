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
            stage 'pull'
            sh 'docker pull alpine'
        }
      }
      stage('confirm') {
        container('dockerd') {
            stage 'view'
            sh 'docker images'
            stage 'view2'
            sh 'docker ps'

        }
      }
    }
  }
}