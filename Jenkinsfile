node{
   stage('SCM Checkout'){
     git 'https://github.com/Arunkumar1120/my-app.git'
   }
	stage('Compile-Package'){

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
	
   stage('Build Docker Imager'){
   sh 'docker build -t hackerarun/myweb:0.0.2 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u hackerarun -p ${dockerPassword}"
    }
   sh 'docker push hackerarun/myweb:0.0.2'
   }

   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
	      stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 65.0.104.240:8083"
   sh "docker tag hackerarun/myweb:0.0.2 65.0.104.240:8083/arun:1.0.0"
   sh 'docker push 65.0.104.240:8083/arun:1.0.0'
	      }
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest hackerarun/myweb:0.0.2' 
   }
}
}
