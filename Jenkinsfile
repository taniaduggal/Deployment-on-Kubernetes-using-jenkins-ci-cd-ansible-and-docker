node {
    stage('Git checkout'){
        git 'https://github.com/taniaduggal/Deployment-on-Kubernetes-using-jenkins-ci-cd-ansible-and-docker.git'
    }
    stage('sending dockerfile/content to ansible server over ssh'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110'
            sh 'scp /var/lib/jenkins/workspace/pipeline-demo/* ubuntu@172.31.1.110:/home/ubuntu'
        }
    }
    stage('Docker Build image'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 cd /home/ubuntu/'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 docker image build -t $JOB_NAME:v1.$BUILD_ID .'
    }
    }
    stage('Docker IMAGE TAGGING'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 cd /home/ubuntu/'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 docker image tag $JOB_NAME:v1.$BUILD_ID taniaduggal60/$JOB_NAME:v1.$BUILD_ID'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 docker image tag $JOB_NAME:v1.$BUILD_ID taniaduggal60/$JOB_NAME:latest'
    }
    }
    stage('push images to Dockerhub'){
        sshagent(['ansible_demo']) {
            sh 'docker login -u taniadugggal60 -p ${dockerhubpassword}' 
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 docker image push taniaduggal60/$JOB_NAME:v1.$BUILD_ID'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 docker image push taniaduggal60/$JOB_NAME:latest'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 docker image rm taniaduggal60/$JOB_NAME:v1.$BUILD_ID taniaduggal60/$JOB_NAME:latest'
        }
    }
    stage('copying files to kubernetes'){
        sshagent(['kubernetes_server']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.10.21'
            sh 'scp  /var/lib/jenkins/workspace/pipeline-demo/* ubuntu@172.31.10.21:/home/ubuntu/'
        }
    }
    stage('k8s deployment using ansible'){
        sshagent(['ansible_demo']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 cd /home/ubuntu/'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 ansible -m ping node'
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.1.110 ansible-playbook ansible.yml'
            }
    }
}
