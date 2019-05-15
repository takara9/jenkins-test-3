podTemplate(
  label: 'mypod',
  containers: [
    containerTemplate(name: 'dockerd', image: 'docker:dind', ttyEnabled: true, alwaysPullImage: true, privileged: true,
      command: 'dockerd --host=unix:///var/run/docker.sock --host=tcp://0.0.0.0:2375 --storage-driver=overlay'),
    containerTemplate(name: 'skaffold-insider', image: 'hhayakaw/skaffold-insider:v1.0.0', ttyEnabled: true, command: 'cat')
  ],
  volumes: [
    emptyDirVolume(memory: false, mountPath: '/var/lib/docker')
  ],
  envVars: [
    podEnvVar(key: 'DOCKER_HOST', value: 'tcp://127.0.0.1:2375')
  ] 
) 
{
  node ('mypod') {
    withCredentials([
      usernamePassword(credentialsId: 'docker_id', usernameVariable: 'DOCKER_ID_USR', passwordVariable: 'DOCKER_ID_PSW')
    ]) {
      stage('Run a docker thing') {
        container('dockerd') {
            stage 'Docker thing1'
            sh 'docker pull redis'
        }
      }
      stage('Test skaffold') {
        git 'https://github.com/takara9/cowweb.git'
        container('skaffold-insider') {
          sh """
            docker login --username=$DOCKER_ID_USR --password=$DOCKER_ID_PSW $DOCKER_HOST
            skaffold run -p release
          """
	  }
        }
      }
   }
}
