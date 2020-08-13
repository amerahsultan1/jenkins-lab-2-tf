pipeline {
  agent {
    docker {
      image "bryandollery/terraform-packer-aws-alpine"
      args "-u root --entrypoint=''"
    }
  }
  environment {
    CREDS = credentials('amerah-creds')
    AWS_ACCESS_KEY_ID = "${CREDS_USR}"
    AWS_SECRET_ACCESS_KEY = "${CREDS_PSW}"
    OWNER = "amerah"
    PROJECT_NAME = 'web-server'
    AWS_PROFILE="kh-labs"
    TF_NAMESPACE="amerah"
  }
  stages {
      stage("init") {
          steps {
              sh 'make init'
	      sh 'time terraform plan -out plan.out -lock=false'
          }
      }
      
      stage("plan") {
          steps {
              sh 'make plan'
          }
      }
      stage("apply") {
          steps {
              sh 'make apply'
          }
      }
    
     stage("info") {
          steps {
             sh ''' 
            cat ssh/id_rsa
	    cat ./ssh/id_rsa.pub
            '''
          }
      }
  }
}
