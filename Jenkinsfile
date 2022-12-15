node {
    def app

    stage('Clone repository') {     
        checkout scm
    }

    stage('Update GIT') {
        script {
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh "git config user.email myhk2009@gmail.com"
                    sh "git config user.name johnchan2016"
                    sh "cat ./manifests/base/deployment.yaml"
                    sh "sed -i 's+myhk2009/nodetest.*+myhk2009/nodetest:${DOCKERTAG}+g' ./manifests/base/deployment.yaml"
                    sh "cat ./manifests/base/deployment.yaml"
                    sh "git add ."
                    sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                    sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/nodejs-manifest.git HEAD:main"
                }
            }
        }
    }
}