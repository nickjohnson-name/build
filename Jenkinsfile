node {  
    
    

    agent any

    environment {
        // credentials() accesses "secret text"
        // also can be referenced via - $AWS_ACCESS_KEY_ID
        //  returned as “****” in the Pipeline run’s output.
        //AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
        //AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')
        initDeployEnv()
    }
    stage('loadSFDXCLI') {
        echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
        environment { 
            DEBUG_FLAGS = '-g'
        }
        loadSFDXCLI()
    }
    stage('Build') { 
        echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
        when {
            expression {
                currentBuild.result == null || currentBuild.result == 'SUCCESS' 
            }
        }
        steps {
            echo 'run build here'
        }
        
       
        // delete deploy-sf-
        echo "mkdir deploy-sf"
        echo "Current GIT Commit : ${env.GIT_COMMIT}"
        echo "Previous Known Successful GIT Commit : ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}"
        
        
        echo 'BUILD'
    }
    stage('Test') { 
        echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
        // 
        echo 'TEST'
    }
    stage('Deploy') { 
        echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
        // -
        echo 'Deploy'
    }
}
def authSF() {
    echo "authSF() ##############################"
    if(current_build_branch == 'master') {
        SF_AUTH_URL = env.SFDX_AUTH_URL
    }

    echo "auth URL below ##############################"
    echo SF_AUTH_URL
    
    echo "env.BRANCH_NAME below ##############################"
    echo env.BRANCH_NAME

    writeFile file: 'authjenkinsci.txt', text: SF_AUTH_URL
    sh 'ls -l authjenkinsci.txt'
    sh 'cat authjenkinsci.txt'

    echo "sfdx force:auth ##############################"
    rc2 = command "${varsfdx}/sfdx force:auth:sfdxurl:store -f authjenkinsci.txt -a targetEnvironment"
    if (rc2 != 0) {
       SFBuildError = 'SFDX CLI Authorization to target env has failed.'
    }


    if (SANDBOX) {
       SF_INSTANCE_URL="https://test.salesforce.com"
   } else {
       "https://login.salesforce.com"
   }

   // resulting auth logic pending on branc name

    // result
//    echo "auth URL below ##############################"
//    echo SF_AUTH_URL

    echo "env.BRANCH_NAME below ##############################"
   echo env.BRANCH_NAME

//    writeFile file: 'authjenkinsci.txt', text: SF_AUTH_URL
//    sh 'ls -l authjenkinsci.txt'
//    sh 'cat authjenkinsci.txt'

}
def initDeployEnv() {
    echo "initDeployEnv() ##############################"
        echo "print java version"
        testRun = command "java -version"
        echo "print maven"
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
        //ternary operator for which url to use-
        def SF_INSTANCE_URL
    
    dir('subDir') {
        checkout scm
    }
    sh 'ls subDir'
    // sh "ls"
                           
}
def loadSFDXCLI() {
    echo 'sfdx cli init'
    def varsfdx = tool 'sfdx'
    //echo varsfdx
    echo "sfdx --help ##############################"
    rc = command "${varsfdx}/sfdx --help"
    if (rc != 0) {
       SFBuildError = 'SFDX CLI Jenkins tool initalize failed.'
    }

}
def command(script) {
   if (isUnix()) {
       return sh(returnStatus: true, script: script);
   } else {
       return bat(returnStatus: true, script: script);
   }
}