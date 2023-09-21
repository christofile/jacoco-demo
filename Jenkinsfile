  pipeline {
    agent none
    stages {
      stage('Build'){
        parallel {
          stage("Build for AMD64 platform") {
              agent {
                 kubernetes {
                   yamlFile 'Jenkins-dind-ds-pod-amd64.yaml'
                 }
              }
              steps {
                container('docker') {
                  sh 'sleep 600'
                  sh 'aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 602709463925.dkr.ecr.us-west-2.amazonaws.com'
                  sh 'docker build -t 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01-amd64 .'
                  sh 'docker push 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01-amd64'
                  sleep(3600) 
                }
              }
            }
           stage("Build for ARM64 platform") {
              agent {
                 kubernetes {
                   yamlFile 'Jenkins-dind-ds-pod-arm64.yaml'
                 }
              }
              steps {
                container('docker') {
                  sh 'aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 602709463925.dkr.ecr.us-west-2.amazonaws.com'
                  sh 'docker build -t 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01-arm64 .'
                  sh 'docker push 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01-arm64'
                  sleep(3600) 
                }
              }
          }
        }
      }
      stage('Manifest'){
        agent {
             kubernetes {
               yamlFile 'Jenkins-dind-ds-pod-amd64.yaml'
             }
        }
        steps {
          container('docker') {
           sh 'aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 602709463925.dkr.ecr.us-west-2.amazonaws.com'
           sh 'docker manifest create 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01 --amend 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01-amd64 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01-arm64'
           sh 'docker manifest annotate 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01-amd64 --arch amd64'
           sh 'docker manifest annotate 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01-arm64 --arch arm64'
           sh 'docker manifest push 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01'
           sh 'docker manifest inspect 602709463925.dkr.ecr.us-west-2.amazonaws.com/java-demo:20230902-01'
          }
        }
      }
    }
}
