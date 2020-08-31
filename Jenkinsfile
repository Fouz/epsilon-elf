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
    image: bryandollery/terraform-packer-aws-alpine
    command: 
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  - name: kubectl
    image: bryandollery/terraform-packer-aws-alpine
    command:
    - cat
    tty: true
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock  
      type: Socket
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
                      kubectl apply -f elf.namespace.yaml
                      kubectl apply -f ingress.yaml -n elf
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
                     helm install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf
                     helm install fluent-bit fluent/fluent-bit --namespace=elf
                     helm install kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=NodePort

                   '''
                          
          }
      }
   }
 }
}
