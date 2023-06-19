
def containerName = "insure-me"
def tag = "latest"
def dockerHubUser = "adhi7862"
def httpPort = "8081"

node {
    stage('Checkout') {
        checkout scm
    }

    stage('Build') {
        sh "mvn clean install"
    }

    stage("Image Prune") {
        sh "docker image prune -a -f"
    }

    stage('Image Build') {
        sh "docker build -t $containerName:$tag --no-cache ."
        echo "Image build complete"
    }

    stage('Push to Docker Registry') {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            sh "docker login -u $dockerUser -p $dockerPassword"
            sh "docker tag $containerName:$tag $dockerUser/$containerName:$tag"
            sh "docker push $dockerUser/$containerName:$tag"
            echo "Image push complete"
        }
    }
    stage('Configure and Deploy to the test-server'){
        ansiblePlaybook become: true, credentialsId: 'ansible-key', disableHostKeyChecking: true, installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: 'ansible-playbook.yml'
    }
}
