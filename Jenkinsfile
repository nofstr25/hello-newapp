def branch = env.BRANCH_NAME
def build = env.BUILD_NUMBER
def appname = "helloworld"
def artifactory = "docker.io" 
def repo = "elevy99927" 
def appimage = "${repo}/${appname}"
def apptag = "${build}"

podTemplate(containers: [
      containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent', ttyEnabled: true),
      containerTemplate(name: 'docker', image: 'gcr.io/kaniko-project/executor:v1.23.0-debug', command: '/busybox/cat', ttyEnabled: true)
  ],
  volumes: [
     configMapVolume(mountPath: '/kaniko/.docker/', configMapName: 'docker-cred')
  ])  {
    node(POD_LABEL) {
        stage('checkout') {
            container('jnlp') {
                sh '/usr/bin/git config --global http.sslVerify false'
                checkout scm
            }
        }

        stage('build') {
            container('docker') {
                echo "Building docker image with Kaniko..."
                sh "/kaniko/executor --force --context=dir://${env.WORKSPACE} --destination=${appimage}:${apptag}"
            }
        }
        stage ('helm install'){
            container('jnlp') {
                //demo step. you will need to update the podTemplate, and use image with k8s utils
                sh "curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3"
                sh "chmod 700 get_helm.sh"
                sh "./get_helm.sh"

            }
        }
        stage('deploy') {
            container('jnlp') {
                sh "echo helm upgrade --install myapp ./my-chart --set image.repository=${appimage} --set image.tag=${apptag}"
            }
        }
    }
}
