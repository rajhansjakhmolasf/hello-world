//lines 1-25 pretty basic for a CICD setup. The new stage for certifcate starts after line# 25

pipeline {
agent any
stages {
stage('Build Application') {
steps {
sh 'mvn clean install'
}
}
stage('Test') {
steps {
echo 'Application in Testing Phase…'
sh 'mvn test'
}
}
stage('Deploy CloudHub') {
environment {
ANYPOINT_CREDENTIALS = credentials('anypointPlatform')
}
steps {
echo 'Deploying mule project due to the latest code commit…'
echo 'Deploying to the configured environment….'
// sh 'mvn package deploy -DmuleDeploy -Dusername=${ANYPOINT_CREDENTIALS_USR} -Dpassword=${ANYPOINT_CREDENTIALS_PSW} -DworkerType=Small -Dworkers=1 -Dregion=us-west-2'
}
}

stage('Certificate Check') {
    steps {
        echo 'Finding certificate'
		script {
			
			//set the URL of mulesoft application which will parse the keytool output
			final String url = "http://hello-world-10072021-123.us-e2.cloudhub.io/api/certficate"
			
			//find jks files in the workspace
			def files = findFiles(glob: '**/*.jks')
			
			//this focuses only on the first jks found with [0]. Please loop into all the files and do the below. 
			//When there are no certificate skip the below
			echo """${files[0].name} ${files[0].path}"""
			
			//certificate found, running keytool now
			def certDetails = sh(script : "keytool -list -v -keystore ${files[0].path} -storepass 123456789", returnStdout: true)
			
			//comment the below later 
			echo "output : ${certDetails}"
			
			
            def response = sh(script: "curl -X POST $url --header 'Content-Type: application/java' --header 'fileName: test1.jks' --header 'appName: customer-prc-api'  --header 'envName: Sandbox' --header 'orgName: Personal' --data-raw $certDetails", returnStdout: true)
            echo response
			
	
   		}
	}

}
}
}
