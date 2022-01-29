pipeline {

	agent any

	stages 
	{
		stage ("Build")
		{
			steps {
			// Building artifact
				sh '''
				docker build -t hello-world:jenkins .
				docker run -p 80 --name jenkins-test -dt hello-world:jenkins
				'''
			}
		}

		stage ("Test")
		{
			steps {
				sh '''

				// Extracting data 
				IP=$(docker inspect --format '{{ .NetworkSettings.IPAddress }}' jenkins-test)
				STATUS=$(curl -sL -w "%{http_code} \n" $IP:80 -o /dev/null)

				// Testing if site is up
					if [ $STATUS -eq 200 ]; then
					echo "Site is not up, test failed"
					exit 1
					fi
					echo "Site is up, test succeeded"
				'''
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
