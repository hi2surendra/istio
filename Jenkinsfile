def label = "worker-${UUID.randomUUID().toString()}"

podTemplate(label: label, containers: [
  containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true),
  containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.8.8', command: 'cat', ttyEnabled: true),
],
volumes: [
 hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')]
) {
  node(label) {
    def myRepo = checkout scm
    def gitCommit = myRepo.GIT_COMMIT
    def gitBranch = myRepo.GIT_BRANCH
    def shortGitCommit = "${gitCommit[0..10]}"
    def previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)

    def harborHostName = "harbor.sbg.k8s.sbsa.s7s.cloud"
     def project = "productpage"
     def containerName = "productinfocontainer"
    def version = "1.0"

    stage('Create Docker images') {
      container('docker') {
          sh "docker build samples/bookinfo/src/productpage -t ${containerName}:${version}"
          sh "docker tag ${containerName}:${version} ${harborHostName}/${project}/${containerName}:${version}"
          sh "docker login ${harborHostName} -u=admin -p=Harbor12345"
          sh "docker push ${harborHostName}/${project}/${containerName}:${version}"
        }
      }

    stage('Run kubectl') {
      container('kubectl') {
   //   sh "kubectl delete -f samples/bookinfo/platform/kube/productinfo-harbor.yaml"
        sh "kubectl apply -f samples/bookinfo/platform/kube/productinfo-harbor.yaml"
        sh "kubectl apply -f samples/bookinfo/platform/kube/productinfo-vs-gw-dr.yaml"
      }
    }
  }
}
