# build
build





// https://www.jenkins.io/doc/book/pipeline/pipeline-best-practices/#avoiding-very-large-shared-libraries


    /*

        *Making sure to use Groovy code in Pipelines as glue

        Use Groovy code to connect a set of actions rather than 
        as the main functionality of your Pipeline. In other words, 
        instead of relying on Pipeline functionality (Groovy or Pipeline steps) 
        to drive the build process forward, use single steps (such as sh) 
        to accomplish multiple parts of the build. Pipelines, 
        as their complexity increases (the amount of Groovy code, number of steps used, 
        etc.), require more resources (CPU, memory, storage) on the master. 
        Think of Pipeline as a tool to accomplish a build rather than the core of a build.

        *Example: Using a single Maven build step to drive the build through its build/test/deploy process.

        *Avoiding complex Groovy code in Pipelines
        ** NO - JsonSlurper
        -   This function (and some other similar ones like XmlSlurper or readFile) can be used to read from a file on disk, parse the data from that file into a JSON object, and inject that object into a Pipeline using a command like JsonSlurper().parseText(readFile("$LOCAL_FILE")). This command loads the local file into memory on the master twice and, if the file is very large or the command is executed frequently, will require a lot of memory.
        -   Solution: Instead of using JsonSlurper, use a shell step and return the standard out. This shell would look something like this: def JsonReturn = sh label: '', returnStdout: true, script: 'echo "$LOCAL_FILE"| jq "$PARSING_QUERY"'. This will use agent resources to read the file and the $PARSING_QUERY will help parse down the file into a smaller size.
        ** NO - HttpRequest: 
        -   Frequently this command is used to grab data from an external source and store it in a variable. This practice is not ideal because not only is that request coming directly from the master (which could give incorrect results for things like HTTPS requests if the master does not have certificates loaded), but also the response to that request is stored twice.
        -   Solution: Use a shell step to perform the HTTP request from the agent, for example using a tool like curl or wget, as appropriate. If the result must be later in the Pipeline, try to filter the result on the agent side as much as possible so that only the minimum required information must be transmitted back to the Jenkins master.



        *Reducing repetition of similar Pipeline steps

        Combine Pipeline steps into single steps as often as possible to reduce the amount of overhead caused by the Pipeline execution engine itself. For example, if you run three shell steps back-to-back, each of those steps has to be started and stopped, requiring connections and resources on the agent and master to be created and cleaned up. However, if you put all of the commands into a single shell step, then only a single step needs to be started and stopped.

        *Example: Instead of creating a series of echo or sh steps, combine them into a single step or script.
        *Avoiding calls to Jenkins.getInstance




    */



    // Avoiding large global variable declaration files
    // Do not override built-in Pipeline steps
    // Avoiding very large shared libraries
    // Do not assign non-serializable objects to variables -- always infer "just-in-time"

    /*
        Reducing repetition of similar Pipeline steps
        Combine Pipeline steps into single steps as often as 
            possible to reduce the amount of overhead caused by the 
            Pipeline execution engine itself. For example, if you run three 
            shell steps back-to-back, each of those steps has to be started and stopped, 
            requiring connections and resources on the agent and master 
            to be created and cleaned up. However, if you put all of the commands 
            into a single shell step, then only a single step needs 
            to be started and stopped.
        Example: Instead of creating a series of echo or sh steps, 
            combine them into a single step or script.

    */


    // differential build, leveraging an agreed upon naming convention
        // i.e {ClassName}Test.apex - if all unit tests following
        // this naming convention are picked up in a 
        // runspecified sfdx run test command based on if a unit
        // of code changes, its "related" unit testing should be
        // included and ran as part of the build.





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





        /*

wget https://developer.salesforce.com/media/salesforce-cli/sfdx-linux-amd64.tar.xz
tar xfvJ sfdx*
cd sfdx
./install

curl "https://developer.salesforce.com/media/salesforce-cli/sfdx-linux-amd64.tar.xz" -o "sfdx.tar.xz"
mkdir -p sfdx
tar -xvJf sfdx.tar.xz -C sfdx --strip-components=1 

*/

/*
Using environment variables
Jenkins Pipeline exposes environment variables via the global variable env, which is available from anywhere within a Jenkinsfile. The full list of environment variables accessible from within Jenkins Pipeline is documented at ${YOUR_JENKINS_URL}/pipeline-syntax/globals#env and includes:

BUILD_ID
The current build ID, identical to BUILD_NUMBER for builds created in Jenkins versions 1.597+

BUILD_NUMBER
The current build number, such as "153"

BUILD_TAG
String of jenkins-${JOB_NAME}-${BUILD_NUMBER}. Convenient to put into a resource file, a jar file, etc for easier identification

BUILD_URL
The URL where the results of this build can be found (for example http://buildserver/jenkins/job/MyJobName/17/ )

EXECUTOR_NUMBER
The unique number that identifies the current executor (among executors of the same machine) performing this build. This is the number you see in the "build executor status", except that the number starts from 0, not 1

JAVA_HOME
If your job is configured to use a specific JDK, this variable is set to the JAVA_HOME of the specified JDK. When this variable is set, PATH is also updated to include the bin subdirectory of JAVA_HOME

JENKINS_URL
Full URL of Jenkins, such as https://example.com:port/jenkins/ (NOTE: only available if Jenkins URL set in "System Configuration")

JOB_NAME
Name of the project of this build, such as "foo" or "foo/bar".

NODE_NAME
The name of the node the current build is running on. Set to 'master' for master node.

WORKSPACE
The absolute path of the workspace
*/



Referencing or using these environment variables can be accomplished like accessing any key in a Groovy Map, for example:

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Example') {
            steps {
                echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
            }
        }
    }
}