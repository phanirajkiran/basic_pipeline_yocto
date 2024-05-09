#!/usr/bin/env groovy

pipeline {
  options {
    disableConcurrentBuilds()
  }
  agent {
    docker {
      image 'crops/yocto:ubuntu-20.04-base'
      args '-v /var/jenkins:/var/jenkins'
      reuseNode true
    }
  }
  stages {
    stage('Build core-image-minimal') {
      steps {
        sh '''
           mkdir -p build/conf
           cat << EOF > build/conf/local.conf

SSTATE_DIR = "/var/jenkins/bbcache/sstate"
DL_DIR = "/var/jenkins/bbcache/downloads"
BB_SIGNATURE_HANDLER = "OEEquivHash"
BB_HASHSERVE = "hashserv-rw:8686"
MACHINE = "qemux86-64"
DISTRO = "poky"
INHERIT += "rm_work buildhistory cve-check"
BUILDHISTORY_COMMIT = "0"
BB_NUMBER_THREADS = "2"
EOF

           . ./oe-init-build-env build
           bitbake core-image-minimal
           '''
      }
    }

  }
}
