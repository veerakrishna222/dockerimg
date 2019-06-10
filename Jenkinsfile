
node('pro1') {
// Delete the workspace
//deleteDir()
     def app
stage('Retrieve source code') {
    checkout scm
    delivery = load 'repository.groovy'
    sh " cd $WORKSPACE;/bin/mkdir Build-${env.BUILD_NUMBER} "
    }
try {
     stage('Maven Build') {
      docker.image('maven:3.5-jdk-8-alpine').inside {
        sh "mvn clean package -Dbuild.number=${BUILD_NUMBER}"
        sh "/bin/mv -f $WORKSPACE/target/*.war $WORKSPACE/Build-${env.BUILD_NUMBER}/vsvyadav_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war"
        sh "/bin/cp -f $WORKSPACE/Build-${env.BUILD_NUMBER}/vsvyadav_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war $WORKSPACE/vsvyadav.war"
       
      }
     }
          stage('build image') {
        app = docker.build("veerakrishna222/kubernates:docker${env.BUILD_NUMBER}")
       }
   
          stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://registry.hub.docker.com', 'docker-veera') {
            app.push("docker${env.BUILD_NUMBER}")
           
         }
          }
    
   stage('Deploy') {
        sh "/bin/cp -f $WORKSPACE/Build-${env.BUILD_NUMBER}/vsvyadav_${env.BRANCH_NAME}${env.BUILD_NUMBER}.war /opt/apache-tomcat-8.5.41/webapps/vsvyadav.war"
        sh " sh /opt/apache-tomcat-8.5.41/bin/startup.sh"
   }
  
   delivery.artifactory()
        
       
  }
  catch (e) {
      currentBuild.result = "FAILED"
      throw e
    }
}

