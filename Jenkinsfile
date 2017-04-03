node {
	checkout scm

	def version

	stage('Preparation') {
		sh 'git rev-parse HEAD > GIT_COMMIT'
		version = readFile('GIT_COMMIT').take(6)
	}

	docker.image("maven:3").inside {
		stage('Build') {
			sh 'mvn clean package'
		}
	}

	stage('Results') {
		archive 'target/clickCount.war'
	}

	stage('Build image') {
		sh "docker build -t registry.swarm.uo.techx.fr/xebiafrance/click-count:${version} ."
	}

	stage('Push image') {
		sh "docker push registry.swarm.uo.techx.fr/xebiafrance/click-count:${version}"
	}

	stage('Deploy on Staging') {
		sh "VERSION=${version} docker -H tcp://swarm-master1.private:2375 stack deploy -c docker-compose.yml click-count"
	}
}