pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
spec:
  containers:
  - name: helm
    image: alpine/helm
    command: 
    - cat
    tty: true
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
"""
    }
  }   

environment {
    TOKEN=credentials('bc33a7c7-818f-47d0-9140-94822190010c')
  }

stages { 
    stage('deploy:kubectl') {
          steps {
              container('kubectl') {
                  sh '''
                       kubectl --token=$TOKEN create namespace elff
                  '''
              }
          }
      }


    stage('deploy:helm') {
            steps {
                container('helm') {
                   sh '''
                     helm repo add elastic https://helm.elastic.co
                     helm repo add fluent https://fluent.github.io/helm-charts
                     helm repo update
                     helm install elasticsearch elastic/elasticsearch --version=7.9.0 
                     helm install fluent-bit fluent/fluent-bit --namespace=elf
                     helm install kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=NodePort

                   '''
                          
          }
      }
   }
 }
}
