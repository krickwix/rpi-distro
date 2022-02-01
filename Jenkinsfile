pipeline {
    agent {
          docker { image 'docker.io/krickwix/ybuild:v0.1' }
    }
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
                    MACHINE=raspberrypi4-64 bitbake gbeos-minimal && \
                    MACHINE=raspberrypi3-64 bitbake gbeos-minimal")
                }
            }
        }
        stage('image-rpi3') {
            steps {
                sh(
                "cd $WORKSPACE/build/tmp-glibc/deploy/images/raspberrypi3-64 && \
                bmaptool copy --bmap gbeos-minimal-raspberrypi3-64.wic.bmap \
                    gbeos-minimal-raspberrypi3-64.wic.bz2 \
                    gbeos-minimal-raspberrypi3-64.img"
                )
            }
        }
	stage('image-rpi4') {
	    steps {
                sh(
                "cd $WORKSPACE/build/tmp-glibc/deploy/images/raspberrypi4-64 && \
                bmaptool copy --bmap gbeos-minimal-raspberrypi4-64.wic.bmap \
                    gbeos-minimal-raspberrypi4-64.wic.bz2 \
                    gbeos-minimal-raspberrypi4-64.img")
           }
	}
        stage("artefacts") {
            steps {
                archiveArtifacts artifacts: 'build/tmp-glibc/deploy/images/**/*.img',
                   allowEmptyArchive: true,
                   fingerprint: true,
                   onlyIfSuccessful: true
                archiveArtifacts artifacts: 'build/tmp-glibc/deploy/images/**/*.wic.bmap',
                   allowEmptyArchive: true,
                   fingerprint: true,
                   onlyIfSuccessful: true
                archiveArtifacts artifacts: 'build/tmp-glibc/deploy/images/**/*.wic.bz2',
                   allowEmptyArchive: true,
                   fingerprint: true,
                   onlyIfSuccessful: true
		minio bucket: 'gbear-yocto-images-raspberrypi',
                   credentialsId: 'minio_gbear-user',
                   targetFolder: 'jenkins-build/',
                   host: 'http://10.60.16.240:9199',
                   includes: 'build/tmp-glibc/deploy/images/**/*.img'
                minio bucket: 'gbear-yocto-images-raspberrypi',
                   credentialsId: 'minio_gbear-user',
                   targetFolder: 'jenkins-build/',
                   host: 'http://10.60.16.240:9199',
                   includes: 'build/tmp-glibc/deploy/images/**/*.wic.bmap'
                minio bucket: 'gbear-yocto-images-raspberrypi',
                   credentialsId: 'minio_gbear-user',
                   targetFolder: 'jenkins-build/',
                   host: 'http://10.60.16.240:9199',
                   includes: 'build/tmp-glibc/deploy/images/**/*.wic.bz2'
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
