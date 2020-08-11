pipeline {

    agent {
        docker {
            image 'bryandollery/terraform-packer-aws-alpine'
            args "-u root"
        }
    }
    stages {
        stage ('generate manifest') {
            steps {
                sh """
cat <<EOF > ./manifest.txt
name: ${JOB_NAME}
time: ${currentBuild.startTimeInMillis}
build #: ${BUILD_NUMBER}
commit: ${GIT_COMMIT}
url: ${GIT_URL}
EOF
"""
            }
        }
        stage ('build') {
            steps {
                sh "docker build --tag manifest-holder:latest ."
                sh "docker tag manifest-holder manifest-holder:${BUILD_NUMBER}"
                sh "docker tag manifest-holder bryandollery/manifest-holder:latest"
                sh "docker tag manifest-holder bryandollery/manifest-holder:${BUILD_NUMBER}"
            }
        }
        stage ('test') {
            steps {
                sh "docker run --rm manifest-holder"
            }
        }
        stage ('release') {
            environment {
                CREDS = credentials('amerah-creds')
            }
            steps {
                sh "whoami"
                sh "docker login -u ${CREDS_USR} -p ${CREDS_PSW}"
                sh "docker push bryandollery/manifest-holder:${BUILD_NUMBER}"
                sh "docker push bryandollery/manifest-holder:latest"
            }
        }
    }
}
