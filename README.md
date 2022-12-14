# nodejs-manifest

## 1. Jenkins installation 
	a. Install jenkins in EC2
		i. Reference: https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/		
		ii. Install Docker
		iii. Run ```sudo chmod 666 /var/run/docker.sock``` on the EC2 after Docker is installed.
		
	b. Jenkins plugins
		- Docker plugin
		- Docker Pipeline
		- Locale
		
	c. Config credentials
		- github
		- dockerhub
	

## 2. Setup 2 pipelines
	a. for building image [nodejs-code](https://github.com/johnchan2016/nodejs-code.git)
	
		i. use nodejs for Web server
		
		ii. init the project
		
		iii. create a Dockerfile for building nodejs image
		
		```
			FROM node:16-alpine
			ENV APP_ENV=dev

			WORKDIR /app

			COPY ["package.json", "package-lock.json*", "./"]

			RUN npm install

			COPY . .

			CMD [ "node", "app.js" ]
		```
		
		iv. create a jenkinsfile for the pipeline		
			- create a stage for building image			
			- after image push to the dockerhub, trigger another pipeline to update k8s manifest
			
			```
			  stage('Trigger ManifestUpdate') {
						echo "triggering updatemanifestjob"
						build job: 'UpdateNodeManifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
				}
			```
	
	b. for update k8s manifest [nodejs-manifest](https://github.com/johnchan2016/nodejs-manifest.git)
	
		i. update image version in deployment.yaml
		
		ii. commit the changes
	
## 3. Setup Argo CD for deployment
	[Reference](https://www.eksworkshop.com/intermediate/290_argocd/)
	
	a. setup a EKS cluster using AWS cloudformation
	
	b. install Argo CD 
	
	c. setup application inside Argo CD for automated deployment 
	
		[Reference[(https://argo-cd.readthedocs.io/en/stable/getting_started/#6-create-an-application-from-a-git-repository)
	