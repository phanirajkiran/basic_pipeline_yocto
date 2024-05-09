pipeline {
    agent any

    stages {
        stage('clone poky dunfell') {
            steps {
            sh '''#!/bin/bash
                set -x
                rm -fr *
                git clone git://git.yoctoproject.org/poky
                cd poky
                git checkout -b dunfell origin/sumo
                '''
            }
        }
        stage('fetch sources for build') {
            steps {
            sh '''#!/bin/bash
                set -x
                cd poky
                source oe-init-build-env
                # ln -s /media/data/.yoctoshare/downloads ./downloads
                bitbake core-image-minimal --runall=fetch
                '''
            }
        }
        stage('build poky image') {
            steps {
            sh '''#!/bin/bash
                set -x
                cd poky
                source oe-init-build-env
                bitbake core-image-minimal
                '''
            }
        }
    } 
}
