pipeline {
  agent {
    kubernetes {
      label 'kaniko'
      yaml """
kind: Pod
metadata:
  name: kaniko
spec:
  containers:
  - name: jnlp
    workingDir: /home/jenkins
  - name: kaniko
    workingDir: /home/jenkins
    image: gcr.io/kaniko-project/executor:debug
    imagePullPolicy: Always
    command:
    - /busybox/cat
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
"""
    }
  }
  stages {
    stage('Build with Kaniko') {
      
      steps {
        git 'https://github.com/lukasz-rasinski/ecsdemo-nodejs'

        container(name: 'kaniko') {        
          sh """
          #!/busybox/sh
          /kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --verbosity debug --destination lrasinski/ecsdemo-nodejs:${env.BUILD_ID} --destination lrasinski/ecsdemo-nodejs:latest
          """
        }
      }
    }
  }
}