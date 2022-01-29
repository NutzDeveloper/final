pipeline {

	agent any

	stages 
	{
		stage ("Preperation")
		{
			steps {
				sh '''
				docker build -t hello-world:jenkins .
				docker run -p 80 --name jenkins-test -dt hello-world:jenkins
				IP=$(docker inspect --format '{{ .NetworkSettings.IPAddress }}' jenkins-test)
				STATUS=$(curl -sL -w "%{http_code} \n" $IP:80 -o /dev/null)
				'''
			}
		}
	}

	post {
		always {

			sh '''
				echo "Pipeline has ended, deleting image and containers"
				docker stop jenkins-test
				docker rm jenkins-test -f
			'''
		}
	}
}
