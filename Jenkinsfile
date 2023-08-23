pipeline {
    agent { node { label 'jenkins-slave' } }  

    parameters {
        string(name: 'IMAGE_TAG', description: 'Docker image tag for deployment')
    }

    stages {     
        
        stage('Update Helm Chart Image Tag') {
            steps {
                script {

                    // Print the working directory
                    sh 'pwd'

                    // List files in the current directory
                    sh 'ls'

                    def helmChartPath = '/opt/build/workspace/project-x-helm/helm-chart'  // Path to your Helm Chart folder in the Git repo
                    def valuesFilePath = "${helmChartPath}/values.yaml"
                    
                    // Replace the image tag in the values.yaml file
                    sh "sed -i 's/tag:.*/tag: $${IMAGE_TAG}/' ${valuesFilePath}"
                    
                    // Configure Git to use provided credentials
                    withCredentials([usernamePassword(credentialsId: 'project-x', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh "git -C ${helmChartPath} config --local credential.helper '!f() { echo username=${GIT_USERNAME}; echo password=${GIT_PASSWORD}; }; f'"
                        sh "git -C ${helmChartPath} add ${valuesFilePath}"
                        sh "git -C ${helmChartPath} commit -m 'Update image tag'"
                                                
                        // Push the merged changes
                        sh "git -C ${helmChartPath} push origin HEAD:main"
                    }
                }
            }
        }
    }
}    