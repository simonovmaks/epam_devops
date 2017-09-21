    def repositoryUrl = "https://github.com/simonovmaks/epam_devops.git"
    def gitUrl = "simonovmaks/epam_devops.git"
    def tree = "Task3"
    def gradleProp = "gradle.properties"
    def artifact = "task3.war"
    def WAR = "./build/libs/task3.war"
    def tomwar = "./test.war"
    def GitUser = "simonovmaks"
    def GitUserEmail = "simonovmaxal@gmail.com"
    def tomcatNode = ['node1', 'node2']
    def tomcats = ['tomcat1', 'tomcat2']
node () {
    stage ('git'){
        git branch: tree, url: repositoryUrl
    }
    stage ('build'){
        sh('chmod +x ./gradlew && ./gradlew clean build')
    }
    def propFile = readFile gradleProp
    def version = propFile.substring(8)
    
    stage('nexus') {
        withCredentials([usernamePassword(credentialsId: '0b647aba-efe2-40fe-85d6-3fc0b310d1c9', passwordVariable: 'NEXUS_PASS', usernameVariable: 'NEXUS_U')]) {
            sh "curl -Lv -u ${NEXUS_U}:${NEXUS_PASS} -T ${WAR} \"http://127.0.0.1:8081/nexus/content/repositories/snapshots/${tree}/${version}/\""
} 
    }
    if(tomcats.size()>0){
        int i = 0
        for (tomcat in tomcats){
            stage("Deploy WAR-artifact to "+tomcat.toString()+""){
                def temp = "http://127.0.0.1:80/jkmanager/?cmd=update&from=list&w=balance&sw=${tomcatNode[i]}&vwa=1"
                httpRequest httpMode: 'POST', url: temp
                withCredentials([usernamePassword(credentialsId: '5d8716b7-4557-4f89-9772-dd1f5cf66605', passwordVariable: 'Tom_Pass', usernameVariable: 'Tom_U')]) {
                sh "curl -o test.war \"http://localhost:8081/nexus/content/repositories/snapshots/${tree}/${version}/${artifact}\" | curl -u ${Tom_U}:${Tom_Pass} \"http://${tomcat}:8080/manager/text/undeploy?path=/test\" && curl -XPUT -T ${tomwar} -u ${Tom_U}:${Tom_Pass} \"http://${tomcat}:8080/manager/text/deploy?path=/test&update=true\" && curl -vv -u ${Tom_U}:${Tom_Pass} \"http://${tomcat}:8080/manager/text/reload?path=/test\""
                }
                sh "sleep 20" 
  
                    temp = "http://127.0.0.1:80/jkmanager/?cmd=update&from=list&w=balance&sw=${tomcatNode[i]}&vwa=0"
                    httpRequest httpMode: 'POST', url: temp
                i++
            }
        }
    }

    stage('Push to GIT') {
        withCredentials([usernamePassword(credentialsId: 'd6e8bdf3-0c47-45d8-97bb-4ee83beb6baa', passwordVariable: 'git_pass', usernameVariable: 'git_login')]) {
            sh("git config --global user.name \"${GitUser}\"")
            sh("git config --global user.email \"${GitUserEmail}\"")
            sh("git add ${gradleProp}")
            sh("git commit -am \"Jenkins commit. Build - ${version}.${BUILD_NUMBER}\"")            
            sh("git push https://${git_login}:${git_pass}@github.com/${gitUrl} ${tree}")
        }
    }
}
