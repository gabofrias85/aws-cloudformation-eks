pipeline {
    environment {
        COMMIT_ID = ''
        REGISTRY = 'my-docker-registry'
    }
    agent any
    stages {
        stage ('checkout') {
            steps {
                script {
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'frias-github', url: 'https://github.com/gabofrias85/aws-cloudformation-eks.git']]])
                }
            }
        }

        stage ('Build') {
            steps {
                script {
                    gitCommitHash = sh (script: 'git rev-parse --short HEAD', returnStdout: true)
                    withDockerRegistry([credentialsId: 'frias-dockerhub', url: 'https://index.docker.io/v1/']) {
                        sh "docker build -f api/Dockerfile ./api -t gabrielfriasthoughtworks/basic_example:${gitCommitHash}"
                        sh "docker push gabrielfriasthoughtworks/basic_example:${gitCommitHash}"
                    }
                }
            }
        }
      stage ('K8S Deploy') {
          steps {
                script {
                    gitCommitHash = sh (script: 'git rev-parse --short HEAD', returnStdout: true)
                    COMMIT_ID = gitCommitHash
                    kubernetesDeploy(
                        configs: 'k8s/pipeline_deployment.yaml',
                        kubeconfigId: 'K8S',
                        enableConfigSubstitution: true
                    )
                    // withKubeConfig([credentialsId: 'K8S', serverUrl: 'https://58919550601C3EA7F21AAC0AC9B3ED75.gr7.us-west-2.eks.amazonaws.com']) {
                    //     sh 'kubectl apply -f k8s/pipeline_deployment.yaml'
                    //     }
                }
            }
      }
    }
}
