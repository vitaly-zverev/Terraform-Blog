### Automated Infrastructure (Yandex Cloud) Setup Using Terraform and Jenkins (Launch DataProc)

Inspired by: 
1) [Applying Graph Theory to Infrastructure as Code](https://www.youtube.com/watch?v=Ce3RNfRbdZ0&ab_channel=HashiCorp) 
2) [Automated Infrastructure (AWS) Setup Using Terraform and Jenkins (Launch EC2 and VPC)](https://www.cloudbees.com/blog/terraform-and-jenkins-iac-from-your-build) 
3) [Terraform and Jenkins: IaC from Your Build](https://blogs.perficient.com/2022/06/01/integrating-terraform-with-jenkins-ci-cd/)

```bash
terraform plan
terraform apply
terraform destroy
```
instead of:
```
yc dataproc cluster create dataproc \
   --profile dataproc \
   --bucket=dataproc-bucket \
   --zone=ru-central1-a \
   --service-account-name=dataproc-srv-acc \
   --version=2.0 \
   --services=hdfs,yarn,spark \
   --ssh-public-keys-file=.ssh/id_ed25519.pub \
   --subcluster name=ctrl-subcluster,role=masternode,subnet-name=main-control-plan,assign-public-ip=false \
   --subcluster name=data-subcluster,role=datanode,subnet-name=main-control-plan,hosts-count=3,assign-public-ip=false \
   --deletion-protection=false \
   --ui-proxy=true 
 ```
