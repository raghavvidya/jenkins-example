pipeline {

    agent any 
    
    stages {
        stage("Get Branches from Git"){
            steps{
                     sh """
                        git branch -a | grep remotes > ${WORKSPACE}/branchesList.txt
                     """  
               }
            }
        
        stage('User Input'){
           steps{
               script{
                   listBranchesAvailable = readFile 'branchesList.txt'
                    env.branchToDeploy = timeout (time:1, unit:"HOURS") {
                        input message: 'Branch to deploy', ok: 'Confirm!',
                                parameters: [choice(name: 'Branches Available:', choices: "${listBranchesAvailable }", description: 'Which branch do you want to build?')]
                        }
               }     
           }       
        }
        stage('Checkout Code'){
            steps{
                cleanWs() // ensure workspace is empty
                git branch: "${branchToDeploy}", changelog: false, url: "${GIT_URL}"

            }
        }
        stage("Build") {
            steps{
                echo "Starting Build stage"
            }

        }
    }
}
