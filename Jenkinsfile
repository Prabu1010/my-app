node{

  stage('SCM Checkout'){
    git 'https://github.com/Prabu1010/my-app.git'
  }

  stage('Maven Build'){
    def mvnHome =  tool name: 'maven3', type: 'maven'   
    sh "${mvnHome}/bin/mvn clean package"
    sh 'mv target/myweb*.war target/newapp.war'
  }

  stage('SonarQube Analysis') {
	  def mvnHome =  tool name: 'maven3', type: 'maven'
	  withSonarQubeEnv('sonar') { 
	    sh "${mvnHome}/bin/mvn sonar:sonar"
	  }
	}

  stage('Build Docker Image'){
    sh 'docker build -t prabu1010/myweb:0.0.2 .'
  }

  stage('Docker Image Push'){
    withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
	    sh "docker login -u prabu1010 -p ${dockerPassword}"
    }
	  sh 'docker push prabu1010/myweb:0.0.2'
  }

  stage('Nexus Image Push'){
    sh "docker login -u admin -p nexus987 3.110.50.24:8083"
    sh "docker tag prabu1010/myweb:0.0.2 3.110.50.24:8083/prabu1010:1.0.0"
    sh 'docker push 3.110.50.24:8083/prabu1010:1.0.0'
  }

  stage('Remove Previous Container'){
	  try{
		sh 'docker rm -f tomcattest'
	  }catch(error){
		//  do nothing if there is an exception
	  }
    stage('Docker Deployment'){
    sh 'docker run -d -p 8090:8080 --name tomcattest prabu1010/myweb:0.0.2' 
    }
  }

}
