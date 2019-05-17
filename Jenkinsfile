podTemplate(
  label: 'mypod',
  containers: [
    containerTemplate(name: 'dockerd', image: 'docker:dind', ttyEnabled: true, alwaysPullImage: true, privileged: true,
      command: 'dockerd --host=unix:///var/run/docker.sock --host=tcp://127.0.0.1:2375 --storage-driver=overlay'),
    containerTemplate(name: 'kubectl', image: 'bitnami/kubectl:latest', command: 'cat', ttyEnabled: true,
      alwaysPullImage: true, privileged: true)
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
	    stage 'setup'
	    sh 'docker login --username=$DOCKER_ID_USR --password=$DOCKER_ID_PSW'
	    stage 'confirm'
	    sh 'ls -al'
            stage 'build'
            sh 'docker build -t maho/node-express-login:0.1 .'
	    stage 'push'
	    sh 'docker push maho/node-express-login:0.1'
        }
      }
      stage('confirm') {
        container('dockerd') {
            stage 'view'
            sh 'docker images'
        }
      }
      stage('deploy') {
          container('kubectl') {
              stage('version') {
	          steps {
		      sh 'ls -al'
		      sh 'echo "test" > test.txt'
                      sh 'kubectl version'
                  }
              }
          }
      }
    }
  }
}
