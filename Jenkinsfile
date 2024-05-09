#!/usr/bin/env groovy

pipeline {
  options {
    disableConcurrentBuilds()
  }
  agent {
    docker {
      image 'crops/yocto:ubuntu-20.04-base'
      args '-v /var/jenkins:/var/jenkins'
      label 'yocto'
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
    stage('Archive artifacts') {
      steps {
        zip archive: true, dir: 'build/buildhistory', exclude: '', glob: '', zipFile: 'build/buildhistory.zip'
        zip archive: true, dir: 'build/tmp/log', exclude: '', glob: '', zipFile: 'build/log.zip'
        archiveArtifacts artifacts: 'build/tmp/deploy/images/**/*', fingerprint: true, followSymlinks: false
      }
    }
    stage('Publish release') {
      when {
        buildingTag()
      }
      environment {
        PUBLISH_DIR = "/var/jenkins/releases/${env.JOB_NAME}/${env.CI_BUILD_ID}/"
      }
      steps {
        sh '''
           mkdir -p ${PUBLISH_DIR}
           cp -R --no-dereference --preserve=links build/buildhistory ${PUBLISH_DIR}
           cp -R --no-dereference --preserve=links build/tmp/deploy/cve ${PUBLISH_DIR}
           cp -R --no-dereference --preserve=links build/tmp/deploy/images ${PUBLISH_DIR}
           cp -R --no-dereference --preserve=links build/tmp/deploy/licenses ${PUBLISH_DIR}
           cp -R --no-dereference --preserve=links build/tmp/log ${PUBLISH_DIR}
           '''
      }
    }
  }
}
