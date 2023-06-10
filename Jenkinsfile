pipeline {
    agent{node agent_label }   // like 'master', 'linux' etc.
    tools {
        terraform terraform_distro_label  // like 'v1.4.6_amd64_linux'
    }

    environment { 
        YC_CLOUD_ID = credentials('YC_CLOUD_ID')
        YC_FOLDER_ID = credentials('YC_FOLDER_ID')
        YC_ACCOUNT_KEY_FILE = credentials('YC_ACCOUNT_KEY_FILE') 
        
        TF_VAR_cloud_id=credentials('YC_CLOUD_ID')
        TF_VAR_folder_id=credentials('YC_FOLDER_ID')
        
        TF_VAR_token='just_a_stub'
    }    

    stages {
        stage('Checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/vitaly-zverev/Terraform-Blog.git']]])            

          }
        }
        
        stage ("terraform init") {
            steps {
                sh ('terraform providers lock -net-mirror=https://terraform-mirror.yandexcloud.net -platform=linux_amd64 -platform=darwin_arm64 yandex-cloud/yandex')
                sh ('terraform init') 
            }
        }


        stage ("create token") {
            steps {

                echo "Create token"
                
                sh ('''
                     curl --silent https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash -s -- -i yandex-cloud -n

                     yandex-cloud/bin/yc config set service-account-key \$YC_ACCOUNT_KEY_FILE
                     
                     yandex-cloud/bin/yc iam create-token > token
                     
                  '''
                  ) 
            }
        }        
        
        stage ("terraform plan") {
            steps {
                echo "Terraform plan"
                sh ('''

                     TF_VAR_token=$(cat token) 
                     set | grep TF_VAR
                     terraform plan -no-color -out plan
                     
                     #cat plan
                  '''
                  ) 
           }
        }
        
        stage ("terraform apply ") {
            steps {
                echo "Terraform apply"
                sh ('''
                     TF_VAR_token=$(cat token) 
                     set | grep TF_VAR
                     terraform apply -input=false -auto-approve -no-color "plan" 
                     
                     #cat plan
                  '''
                  ) 
           }
        }
        

        stage ("terraform show ") {
            steps {
                echo "Terraform show"
                sh ('''

                     TF_VAR_token=$(cat token) 
                     set | grep TF_VAR
                     terraform show -no-color  
                     
                     #cat plan
                  '''
                  ) 
           }
        }



        stage ("terraform destroy ") {
            steps {
                echo "Terraform destroy"
                sh ('''

                     TF_VAR_token=$(cat token)  
                     set | grep TF_VAR
                     terraform  apply -destroy -auto-approve -no-color 
                     
                     #cat plan
                  '''
                  ) 
           }
        }
        
    }

    post {
        always {
            cleanWs()
    }
  }

}