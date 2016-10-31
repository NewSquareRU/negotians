node {
  def project = 'pinacta'
  def projectId = '148111'
  def appName = 'negotians'
  def feSvcName = "${appName}"
  def version = sh(script: "cat *.cabal | grep '^version:' | sed 's/[[:alpha:][:space:]|:]//g'", returnStdout: true)
  def imageTag = "gcr.io/${project}-${projectId}/${appName}:${version}"
  def protocVersion = "3.1.0"
  def protoc = "protoc-${protocVersion}"

  checkout scm

  stage 'Load git submodules'
  sh("git submodule init")
  sh("git submodule update")

  stage 'Install protoc'
  sh("wget https://github.com/google/protobuf/releases/download/v${protocVersion}/${protoc}-linux-x86_64.zip")
  sh("unzip ${protoc}-linux-x86_64.zip")

  stage 'Install GHC'
  sh("stack setup")

  stage 'Build project'
  withEnv(["bin"]) {
    sh("stack build")
  }
  stage 'Run tests'
  withEnv(["bin"]){
   sh("stack test")
  }

  stage 'Create docker image'
  sh("stack image container")

  stage 'Create tag of image'
  sh("docker tag ${project}/${appName} ${imageTag}")

  stage 'Push image to registry'
  sh("gcloud docker push ${imageTag}")
}
