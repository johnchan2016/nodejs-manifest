# nodejs-manifest

### 1. Jenkins installation 
a. [Install jenkins in EC2](https://www.jenkins.io/doc/tutorials/tutorial-for-installing-jenkins-on-AWS/)
i. Install Docker
ii. Run ```sudo chmod 666 /var/run/docker.sock``` on the EC2 after Docker is installed.
		
b. Install Jenkins plugins
	- Docker plugin
	- Docker Pipeline
	- Locale
		
c. Config credentials
    - github
    - dockerhub
	

### 2. Setup 2 pipelines
a. For building image [nodejs-code](https://github.com/johnchan2016/nodejs-code.git)
i. Use nodejs for Web server
ii. Init the project
iii. Create a Dockerfile for building nodejs image
```
    FROM node:16-alpine
	ENV APP_ENV=dev

	WORKDIR /app

	COPY ["package.json", "package-lock.json*", "./"]

	RUN npm install

	COPY . .

	CMD [ "node", "app.js" ]
```
	
iv. Create a jenkinsfile for the pipeline		
	- Create a stage for building image			
	- Trigger another pipeline to update k8s manifest
	
	  stage('Trigger ManifestUpdate') {
				echo "triggering updatemanifestjob"
				build job: 'UpdateNodeManifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
		}

	
b. for update k8s manifest [nodejs-manifest](https://github.com/johnchan2016/nodejs-manifest.git)
	i. Update image version in deployment.yaml
	ii. Commit the changes
	
### 3. Setup Argo CD for deployment
a. Setup a EKS cluster using AWS cloudformation
b. [Install Argo CD](https://www.eksworkshop.com/intermediate/290_argocd/)
c. [Setup application](https://argo-cd.readthedocs.io/en/stable/getting_started/#6-create-an-application-from-a-git-repository) inside Argo CD for automated deployment 
	