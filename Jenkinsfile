node ('haimaxy-jnip'){
  stage('Clone') {
    echo "1.Clone Stage"
    checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/en-en/JenkinsTest.git']]])
    script {
        build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
		  if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
    }
    
  }
  stage('Test') {
    echo "2.Test Stage"
  }
  stage('Build') {
    echo "3.Build Stage"
    sh "docker build -t 39.104.110.227:8031/k8sdemo/jenkins-demo:${build_tag} ."
    
  }
  
  stage('Push') {
    echo "4.Push Docker Image Stage"
        withCredentials([usernamePassword(credentialsId: 'harbor', passwordVariable: 'harborPassword', usernameVariable: 'harborUser')]) {
        sh "docker login -u ${harborUser} -p ${harborPassword} 39.104.110.227:8031"
        sh "docker push 39.104.110.227:8031/k8sdemo/jenkins-demo:${build_tag}"
    }
}

  stage('Deploy') {
    echo "5. Change YAML File Stage"
    
    def userInput = input(
        id: 'userInput',
        message: 'Choose a deploy environment',
        parameters: [
            [
                $class: 'ChoiceParameterDefinition',
                choices: "Dev\nQA\nProd",
                name: 'Env'
            ]
        ]
    )
	sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' jenkins.yaml"
	
    sh "sed -i 's/<BUILD_TAG>/${build_tag}/' jenkins.yaml"

    
       if (userInput.Env == "Dev") {
      // deploy dev stuff
    } else if (userInput.Env == "QA"){
      // deploy qa stuff
    } else {
      // deploy prod stuff
    }
    
     sh "kubectl apply -f jenkins.yaml"
    
}
  

}