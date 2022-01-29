pipeline {

	agent any

	stages {
	stage ("Preperation")
	{
		steps {
			docker build -t hello-world:jenkins .
			docker run -p 80 --name jenkins-test -dt hello-world:jenkins
			IP=$(docker inspect --format '{{ .NetworkSettings.IPAddress }}' jenkins-test)
			STATUS=$(curl -sL -w "%{http_code} \n" $IP:8081 -o /dev/null)

			echo "The container's IP is $IP and the status code is $STATUS"
		}
	}

	stage ("Test")
	{
		steps {
			STATUS=$(curl -sL -w "%{http_code} \n" $IP:8081 -o /dev/null)
			if [ $STATUS -eq "200" ]; then
			exit 1
			fi
			echo "Test passed"

		}
	}

	stage ("Deployment")
	{
		steps {
			echo "Deploying"

		}
	}

	stage ("Report")
	{
		steps {
			echo "Sending report"
		}
	}
