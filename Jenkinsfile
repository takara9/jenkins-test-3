def label = "mypod-${UUID.randomUUID().toString()}"
def workspace = "/tmp/jenkins-${UUID.randomUUID().toString()}"
def yaml = """

apiVersion: v1
kind: Pod
metadata:
  name: label
spec:
  containers:
    - name: dockerd
      image: docker:dind
      tty: true
      imagePullPolicy: Always
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: true
      volumeMounts:
      - mountPath: /var/lib/docker
        name: docker-containe-vol
      command: [ 'cat' ]
    - name: kubectl
      image: bitnami/kubectl:latest
      tty: true
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: true
      volumeMounts:
      - mountPath: /var/lib/docker
        name: docker-containe-vol
      command: [ 'cat' ]
  volumes:
  - name: docker-containe-vol
    emptyDir: {}

"""
  podTemplate(label: label, yaml: yaml) {
    node (label) {
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
              stage 'version'
	      sh 'version'
            }
        }
      }
    }
  }




