pipeline {

	agent any
	environment 
	{
		imagename = "mrred13013/prework"
		registryCreds = 'dockerhub'

	}
	
	stages 
	{
		stage ("Build")
		{
			steps {
			// Building artifact
				sh '''
				docker build -t ${imagename} .
				docker run -p 80 --name jenkins-test -dt ${imagename}
				'''
				}
			}
		}

		stage ("Test")
		{
			steps {
				sh '''

				IP=$(docker inspect --format '{{ .NetworkSettings.IPAddress }}' ${imagename})
				STATUS=$(curl -sL -w "%{http_code} \n" $IP:80 -o /dev/null)

					if [ $STATUS -ne 200 ]; then
					echo "Site is not up, test failed"
					exit 1
					fi
					echo "Site is up, test succeeded"
				'''
			}
		}
		stage ("Store Artifact")
		{

			steps {
				script {
							docker.withRegistry( '', registryCredential ) {
dockerImage.push()
}
				}
			}
		}
		stage ("Deploy to Kubernetes")
		{
			steps {

				echo "Deploy to k8s"
			}
		}
	
	}

	post {
		always {

				echo "Pipeline has ended, deleting image and containers"
			sh '''
				docker stop jenkins-test
				docker rm jenkins-test -f
			'''
		}
	}
}
