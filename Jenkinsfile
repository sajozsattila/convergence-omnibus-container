nodePod { label ->
  runInNode(label) {
    container('node') {
      npmLogin()


      stage('Perpare Docker Build') {
        sh '''
        mkdir docker-build
        cp -a docker/* docker-build
        '''
      }

      stage('NPM Install') {
        sh 'npm install'
      }

      stage('Copy Admin Console') {
        sh 'cp -a node_modules/@convergence-internal/admin-console/www/ docker-build/admin-console'
      }

      stage('Publish') {
        sh 'npm publish dist-internal'
      }
    }
  }
}

sbtPod { label ->
  runInNode(label) {
    stage('Fetch Server') {
      container('sbt') {
        sd 'sbt fetchServerNode'
      }
    }

    def containerName = "convergence-de"
    stage('Docker Build') {
      container('docker') {
        dir('docker-build') {
          dockerBuild(containerName)
        }
      }
    }

    stage('Docker Push') {
      container('docker') {
        dockerPush(containerName, ["latest", env.GIT_COMMIT])
      }
    }
  }
}
