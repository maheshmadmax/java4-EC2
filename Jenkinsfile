pipeline {
	agent any
	stages {
  		stage('Compile') {
    			steps {
      				sh 'javac *.java'
    			}
  		}

  		stage('Build Jar') {
    			steps {
      				sh 'jar cvfm sample.jar  manifest.txt *.class'
    			}
 		}

  		stage('Create EC2 instance with TerraForm') {
    			steps {
      				// Creates EC2 T2.micro instance with Tomcat installed on it
				sh("~/terraform init; ~/terraform apply -input=false -auto-approve")
				env.public_ip = sh(returnStdout: true, script: "~/terraform output public_ip").trim()
    			}
  		}

  		stage('Deploy Jar to AWS EC2') {
    			steps {
      				// Deploy sample.jar to AWS
				sh 'scp -r /var/lib/jenkins/workspace/balaji/java3/sample.jar -o StrictHostKeyChecking=no -i /home/ubuntu/.aws/demokey ubuntu@${env.public_ip}:/home/ubuntu/'
				sh 'ssh -o StrictHostKeyChecking=no -i /home/ubuntu/.aws/demokey ubuntu@${env.public_ip} "java -jar sample.jar"'
    			}

    			post {
      				always {
        				// Destroys the earlier created EC2 T2.micro instance
					sh("~/terraform destroy -input=false -auto-approve")
      				}
    			}
  		}

	}
}
	
