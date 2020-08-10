node {  
    stage('ENV SETUP') {
        initDeployEnv()
        
    }
    stage('Build') { 
        // differential build, leveraging an agreed upon naming convention
        // i.e {ClassName}Test.apex - if all unit tests following
        // this naming convention are picked up in a 
        // runspecified sfdx run test command based on if a unit
        // of code changes, its "related" unit testing should be
        // included and ran as part of the build.
       
        // delete deploy-sf
        echo 'mkdir deploy-sf'
        echo 'Current GIT Commit : ${env.GIT_COMMIT}'
        echo 'Previous Known Successful GIT Commit : ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}'
        //def newGit = git diff  ${env.GIT_COMMIT} ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}
        //echo newGit
        // for(currentDiff : newGit.line.separatorDelimiter) {
        //    if(currentDiff != null) {
        //        if(currentDiff == 'force-app/') {
        //            def currentDiffWithoutSRC = currentDiff.trim('force-app/{fileName}')
        //            echo 'Current Component : ${currentDiffWithoutSRC}'
        //            sh 'cp currentDiffWithoutSRC deploy-sf/'
        //            sh 'cp currentDiffWithoutSRC + "-meta.xml"'
        //            sh 'cp currentDiffWithoutSRC + "Test"'
        //            sh 'cp currentDiffWithoutSRC + "Test-meta.xml"'
                   

        //        }
        //    }                 
        // }
        
        echo 'BUILD'

        
    }
    stage('Test') { 
        // 
        echo 'TEST'
    }
    stage('Deploy') { 
        // -
        echo 'Deploy'
    }
}
def initDeployEnv() {
    echo 'print java version'
    testRun = command "java -version"
    echo 'print maven'
    testRun2 = command "mvn --version"
                    
    echo 'test print env variables'
    echo sh(returnStdout: true, script: 'env')

    echo "${BUILD_URL}"

    def current_build_branch = env.BRANCH_NAME
    echo 'CURRENT BUILD BRANCH NAME'
    echo current_build_branch
    echo " #################BEGIN##################"
    echo "##################CHANGE_TARGET_VALIDATION 1################"
    def CHANGE_TARGET = env.CHANGE_TARGET
    echo "##################BEGIN##################"
    echo "##################testing################"
    def SF_AUTH_URL
    def SANDBOX = true
    def VALIDATE_ONLY = false


    def DEPLOYDIR="/var/lib/jenkins/workspace/new_pipeline_${env.BRANCH_NAME}/subDir/force-app/main/default"
    echo DEPLOYDIR

    def wk1 = env.WORKSPACE

    // def TEST_LEVEL='RunLocalTests'
   def TEST_LEVEL='NoTestRun'
   //ternary operator for which url to use
   def SF_INSTANCE_URL
  
   if (SANDBOX) {
       SF_INSTANCE_URL="https://test.salesforce.com"
   } else {
       "https://login.salesforce.com"
   }

   def toolbelt = tool 'toolbelt'
   echo toolbelt

   dir('subDir') {
       checkout scm
   }
   sh 'ls subDir'
   sh "ls"

   // resulting auth logic pending on branc name

    // result
   echo "auth URL below ##############################"
   echo SF_AUTH_URL

    echo "env.BRANCH_NAME below ##############################"
   echo env.BRANCH_NAME

   writeFile file: 'authjenkinsci.txt', text: SF_AUTH_URL
   sh 'ls -l authjenkinsci.txt'
   sh 'cat authjenkinsci.txt'


                           
}
def command(script) {
   if (isUnix()) {
       return sh(returnStatus: true, script: script);
   } else {
       return bat(returnStatus: true, script: script);
   }
}