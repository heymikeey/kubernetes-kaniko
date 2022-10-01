pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        metadata:
          labels:
            some-label: some-label-value
        spec:
          containers:
          - name: nodejs
            image: node:16-alpine
            command:
            - sleep
            args:
            - 99d
            tty: true
          - name: kaniko
            image: gcr.io/kaniko-project/executor:debug
            command:
            - sleep
            args:
            - 9999999
            tty: true
            volumeMounts:
              - name: jenkins-docker-cfg
                mountPath: /kaniko/.docker
          volumes:
          - name: jenkins-docker-cfg
            projected:
              sources:
              - secret:
                  name: regcred
                  items:
                    - key: .dockerconfigjson
                      path: config.json
        '''
    }
  }
  
  stages {
    stage('Run nodejs') {
      steps {
        container('nodejs') {
          sh 'node --version'
        }
      }
    }
    stage('Build with kaniko') {
        steps {
          container('kaniko') {
            sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=$DOCKERHUB_REGISTRY'
          }
        }
    }
  }
}
