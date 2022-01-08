pipeline {
    agent { label 'yocto'}
    stages {
        stage('scm') {
            steps {
                withEnv(['LANG=C']) {
                    sh("git submodule update --init --recursive --jobs 32")
                }
            }
        }
        stage("build") {
            steps {
                withEnv(['LANG=C']) {
                    sh(". setupenv && \
                    MACHINE=raspberrypi4-64 bitbake gbeos-dev && \
                    MACHINE=raspberrypi3-64 bitbake gbeos-dev")
                }
            }
        }
        stage('image') {
            steps {
                sh(
                "cd $WORKSPACE/build/tmp/deploy/images/raspberrypi4-64 && \
                bmaptool copy --bmap gbeos-dev-raspberrypi4-64.wic.bmap \
                    gbeos-dev-raspberrypi4-64.wic.bz2 \
                    gbeos-dev-raspberrypi4-64.img && \
                cd $WORKSPACE/build/tmp/deploy/images/raspberrypi3-64 && \
                bmaptool copy --bmap gbeos-dev-raspberrypi3-64.wic.bmap \
                    gbeos-dev-raspberrypi3-64.wic.bz2 \
                    gbeos-dev-raspberrypi3-64.img"
                )
            }
        }
    }
    post {
        // Clean after build
        always {
            cleanWs()
        }
    }
}