node{
   stage('SCM Checkout'){
     git 'https://github.com/samuelsamits/my-app'
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
   sh 'docker build -t rajesh:0.1.0 .'
   }
    stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u samuelsamits -p ${dockerPassword}"
   sh 'docker push samuelsamits/rajesh:0.1.0'
    }}
    stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 18.215.159.213:8083"
   sh "docker tag samuelsamits/rajesh:0.1.0 18.215.159.213:8083/raja:1.0.0"
   sh 'docker push 18.215.159.213:8083/raja:1.0.0'
   }
    stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
     stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest samuelsamits/rajesh:0.1.0' 
   }
}
}
