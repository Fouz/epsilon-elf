pipeline {
  agent {
    kubernetes {
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
spec:
  containers:
  - name: helm
    image: docker:latest
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
    TOKEN=credentials('75a56777-f2f7-49fe-85ef-593269041c62')
  }

stages { 
    stage("kubectl-deploy") {
          steps {
              container('kubectl') {
                  sh '''
                      kubectl apply -f elf.namespace.yaml
                      kubectl run random-logger --image=chentex/random-logger -n elf
                      kubectl apply -f ingress.yaml -n elf
                  '''
              }
          }
      }


    stage('helm-deploy') {
            steps {
                container('helm') {
                   sh '''
                     helm repo add elastic https://helm.elastic.co
                     helm repo add fluent https://fluent.github.io/helm-charts
                     helm repo update
                     helm install elasticsearch elastic/elasticsearch --version=7.9.0 --namespace=elf
                     helm install fluent-bit fluent/fluent-bit --namespace=elf
                     helm install kibana elastic/kibana --version=7.9.0 --namespace=elf --set service.type=LoadBalancer

                   '''
                          
          }
      }
  }
}
