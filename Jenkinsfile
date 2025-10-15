def appname = "pushed-with-kaniko"
def repo = "nofstr25"  // Replace with your DockerHub username
def artifactory = "docker.io"
def appimage = "${repo}/${appname}"
def apptag = "${env.BUILD_NUMBER}"

podTemplate(containers: [
      containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent', ttyEnabled: true),
      containerTemplate(name: 'kank', image: 'gcr.io/kaniko-project/executor:debug-v0.19.0', command: "/busybox/cat", ttyEnabled: true)
  ],
  volumes: [
     configMapVolume(mountPath: '/kaniko/.docker/', configMapName: 'docker-cred')
  ])  {
    node(POD_LABEL) {
        stage('chackout') {
            container('jnlp') {
            sh '/usr/bin/git config --global http.sslVerify false'
	    checkout scm
          }
        } // end chackout

        stage('build') {
            container('kank') {
            sh """
              /kaniko/executor --force \
                --context ${env.WORKSPACE} \
                --dockerfile ${env.WORKSPACE}/Dockerfile \
                --destination=${artifactory}/${repo}/${appname}:${apptag} \
                --destination=${artifactory}/${repo}/${appname}:latest \
                --skip-tls-verify \
                --cache=true
  """
            }
        } //end build

    //     stage('deploy') {
    //         container('docker') {
	  //     if (DEPLOY) {
    //             echo "***** Doing some deployment stuff *********"
    //          }  else {
    //             echo "***** NO DEPLOY - Doing somthing else. Testing? *********"
    //          }
    //        }
    //     } //end deploy
    // }
    /*
    post {
        always {
            echo 'Pipeline completed!'
        }
        success {
            echo 'Pipeline succeeded!'
            echo 'Send sucess email'
            echo 'Notify CMDB'
        }
        failure {
            echo 'Pipeline failed!'
            echo 'send error email'
        }
    }
    */
}}