pipeline {
    agent{node agent_label }   // like 'master', 'linux' etc.
    tools {
        terraform terraform_distro_label  // like 'v1.4.6_amd64_linux'
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
        
        stage ("terraform plan") {
            steps {
                echo "Terraform plan"
                sh ('''
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