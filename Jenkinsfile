#!/groovy
def dockerImageRepo = 'gatewaytech/gatewaytech-ui'
def dockerImageTag
def dockerImage
def dockerRegistry = 'hub.docker.com'
def bu_nu

pipeline
{
	agent any
	stages
	{
		stage('Cleaning the WorkSpace')
		{
			steps
			{
				deleteDir()
				echo "the build number is ${currentBuild.number}"
				echo 'Cleanup Done'
				// bu_nu="${currentBuild.number}"
				
			}
		}
		
		stage('CheckOut latest Code')
		{
			steps
			{
				checkout scm
				script 
				{

					dockerImageTag="$dockerImageRepo"+":"+"$BUILD_NUMBER"
					echo "Created a Tag for uploading an Image to Registry based on Build_Number : $dockerImageTag"
					sh ' sed -i "s/buildnumber/$BUILD_NUMBER/g" gatewaytech-ui-rs.yaml '
					sh 'cat gatewaytech-ui-rs.yaml'

				}
			}
		}

		stage('Build the Image')
		{
			steps
			{
				script 
				{
					echo 'Starting the Image Building'
					dockerImage = docker.build "${dockerImageTag}"
					sh 'docker images'
					sh 'docker ps -a'
					echo "$dockerImage"
				}
			}
		}

		stage('Publish Docker Images to DockerHub')
		{
			steps
			{
				echo "Pushing Docker image to Registory"
				script
				{
					sh 'docker login --username="anandgit71" --password="anandgit12" ${dockerRegistry}'
					dockerImage.push()
					// sh 'docker rmi $(docker images -a -q)'
					sh 'docker images'
					// sh 'docker rmi $dockerImage'
				}
			}
		}

		stage('Remote Deployment')
		{
			steps
			{
				script
				{
					// echo "done"
					sh 'ansible-playbook deploy.yml -i /etc/ansible/inventory -u ubuntu'
					// sh ' sh deploy.sh '
					// sh 'ansible-playbook test.yml -i /etc/ansible/inventory -u ubuntu'
				}
			}
		}

		/*
		post {
        always {
        		sh 'docker rm $dockerImage'
                sh 'docker logout hub.docker.com'
                deleteDir()
            }
        }
        */
	}
}
