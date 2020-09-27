node {
    def MvnHome=tool name: 'maven-3', type: 'maven'
	def MavenCMD="${MvnHome}/bin/mvn"
	def docker=tool name: 'docker', type: 'dockerTool'
	def DockerCMD="${docker}/bin/docker"
    stage('GitHub Repo checkout & Preparation') { 
       git credentialsId: 'GitHub', url: 'https://github.com/Madhuha5/Springapp_bootcamp.git'  
    }
    stage('Maven Build, Unit test & Package') {
                sh "${MavenCMD} clean package"       
    }
    stage('Docker image build & push to Docker Hub') {
        sh "${DockerCMD} --version"
        sh "${DockerCMD} build -t madhuha/myspringbootimage ."
        withCredentials([string(credentialsId: 'DockerHubPass', variable: 'dockerHubPass')]) {
           sh "${DockerCMD} login -u madhuha -p ${dockerHubPass}"     
        }
	    sh "${DockerCMD} push madhuha/myspringbootimage"
    }
    stage('Docker image pull & run') {
	sshagent(['Docker_User_SSH']) {
        sh 'ssh -o StrictHostKeyChecking=no cloud_user@10.128.0.4 docker --version'
		sh 'ssh -o StrictHostKeyChecking=no cloud_user@10.128.0.4 docker stop springbootapp || true'
		sh 'ssh -o StrictHostKeyChecking=no cloud_user@10.128.0.4 docker rm -f springbootapp || true'
		sh 'ssh -o StrictHostKeyChecking=no cloud_user@10.128.0.4 docker run -d -p 8086:8081 --name springbootapp madhuha/myspringbootimage'
        //sh 'docker run -d -p 8086:8081 madhuha/myspringbootimage'
    }
}
}
