pipeline {

	agent any
	environment 
	{
		user = "mrred13013"
		repo = "prework"
		imagename = "${user}/${repo}"
		registryCreds = 'dockerhub'
		containername = "${repo}-test"
	}
	
	stages 
	{
		stage ("Build")
		{
			steps {
			// Building artifact
				sh '''
				docker build -t ${imagename} .
				docker run -p 80 --name ${containername} -dt ${imagename}
				'''
				}
		}

		stage ("Test")
		{
			steps {
				sh '''

				IP=$(docker inspect --format '{{ .NetworkSettings.IPAddress }}' ${containername})
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
				echo "Storing artifact"
				withCredentials([usernamePassword( credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
       				def registry_url = "registry.hub.docker.com/"
        			sh "docker login -u $USER -p $PASSWORD ${registry_url}"
        			docker.withRegistry("http://${registry_url}", "dockerhub") {
              			sh 'docker push ${imagename}:latest'
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
				docker stop ${containername}
				docker rm ${containername} -f
			'''
		}
	}
}
