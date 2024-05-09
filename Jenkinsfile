pipeline {
    agent any
    stages {
        stage('Fetch'){
            steps{
                sh 'echo "Yocto Fetch"'
                dir ('poky') {
                    git branch: 'sumo', url: 'http://git.yoctoproject.org/git/poky.git'
                    dir ('meta-example') {
                        git branch: 'master', url: 'https://github.com/abhinema/meta-example.git'
                    }
                }
            }
        }
        stage('Build') {
            steps {
               dir ('poky') {
                sh '''bash -c "export BB_NUMBER_THREADS=64 && export IMAGE_INSTALL_append=cpcmd && \
                        source oe-init-build-env && \
                        rpl -i -w "PACKAGE_CLASSES ?= \"package_rpm\"" "#PACKAGE_CLASSES ?= \"package_rpm\"" local.conf &&\
                        bitbake-layers add-layer ../meta-example && \
                        bitbake -c cleansstate minimal-example &&\
                        bitbake minimal-example" '''
                }
            }
        }            
    }
}
