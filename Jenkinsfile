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
    }
  }
}

sbtPod { label ->
  runInNode(label) {
    injectIvyCredentials();
    
    stage('Fetch Server') {
      container('sbt') {
        sh 'sbt fetchServerNode'
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