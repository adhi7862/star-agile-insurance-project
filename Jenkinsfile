
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
    stage("Exicute Ansible") {
        ansiblePlaybook credentialsId: 'private-key', disableHostKeyChecking: true, installation: 'ansible', inventory: 'ansible.init', playbook: 'ansible-playbook.yml'
    }
    stage('execute test-server') {
        sh 'java -jar Desktop.jar'
    }
}
