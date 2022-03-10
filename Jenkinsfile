pipeline {

    agent any 
    
    stages {
        stage("Get Branches from Git"){
            steps{
                     sh """
                        git tag > ${WORKSPACE}/TagList.txt
                     """  
               }
            }
        
        stage('User Input'){
           steps{
               script{
                   listBranchesAvailable = readFile 'TagList.txt'
                    env.branchToDeploy = timeout (time:1, unit:"HOURS") {
                        input message: 'Tag to deploy', ok: 'Confirm!',
                                parameters: [choice(name: 'Tags Available:', choices: "${listBranchesAvailable }", description: 'Which tags do you want to build?')]
                        }
               }     
           }       
        }
        stage('Checkout Code'){
            steps{
                cleanWs() // ensure workspace is empty
             //   git branch: "${branchToDeploy}", changelog: false, url: "${GIT_URL}"
              //  checkout([$class: 'GitSCM', branches: [[name: 'refs/tags/${branchToDeploy}']], userRemoteConfigs: [[url: ${GIT_URL}]]])
                  checkout([
                        $class: 'GitSCM',
                        branches: [[name: '${branchToDeploy}']],
                        extensions: [[$class: 'CloneOption', shallow: false, depth: 0, reference: '']],
                        userRemoteConfigs: scm.userRemoteConfigs,
                ])
            }
        }
        
        
        stage ('Compile Stage') {

            steps {
                withMaven(maven : 'maven_3_5_0') {
                    sh 'mvn clean compile'
                }
            }
        }
        stage ('Testing Stage') {

            steps {
                withMaven(maven : 'maven_3_5_0') {
                    sh 'mvn test'
                }
            }
        }
        stage ('Deployment Stage') {
            steps {
                withMaven(maven : 'maven_3_5_0') {
                    sh 'mvn deploy'
                }
            }
        }
    }
}
