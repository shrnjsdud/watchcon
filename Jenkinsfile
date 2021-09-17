node {
    
    try{
        
    slackSend (channel: '#cicd', color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")

     def VERSION = '1.0'  



        
    
     
     stage('Clone Build repository') {
         checkout scm
     }     
     
        
     stage('Push image') {
         app = docker.build("nky/watchcon")
         docker.withRegistry('https://repo.nky.wjcloud.co.kr', 'harbor') {
             app.push("${VERSION}")
             app.push("latest")
         }
     }
      
        
     stage('Clone Deploy repository') {
         sh 'rm -rf deploy'
         sh 'git clone https://github.com/shrnjsdud/deploy.git'
     }   
        
        
     stage('Push manifest') {
          
          dir("deploy"){
              sshagent(credentials : ['jenkins_github_ssh']) {
              sh 'git remote show'
              sh 'git remote remove origin'
              sh 'git remote add origin git@github.com:shrnjsdud/deploy.git'
              sh "cd overlays/dev && kustomize edit set image repo.nky.wjcloud.co.kr/nky/wj-watchcon:${VERSION}"
              sh 'cd overlays/dev && kustomize build > ../../deploy.yaml'
              sh 'git add .'
              sh 'git status'
              sh 'git commit -m "deploy"'
              sh 'git push origin master' 
                   
              }  
          }
     }
    
     slackSend (channel: '#cicd', color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")    
     }catch(e){
     slackSend (channel: '#cicd', color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
     }


 }
