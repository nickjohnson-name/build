# build
build
sfdx force:alias:list
sfdx force:org:display -u Hub_Org --verbose

# https://github.com/jenkinsci/pipeline-examples


# https://www.jenkins.io/doc/book/pipeline/pipeline-best-practices/

// NULL checks example: auth url variable if null
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




Setting environment variables
Setting an environment variable within a Jenkins Pipeline is accomplished differently depending on whether Declarative or Scripted Pipeline is used.

Declarative Pipeline supports an environment directive, whereas users of Scripted Pipeline must use the withEnv step.

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    environment { 
        CC = 'clang'
    }
    stages {
        stage('Example') {
            environment { 
                DEBUG_FLAGS = '-g'
            }
            steps {
                sh 'printenv'
            }
        }
    }
}






Setting environment variables dynamically
Environment variables can be set at run time and can be used by shell scripts (sh), Windows batch scripts (bat) and PowerShell scripts (powershell). Each script can either returnStatus or returnStdout. More information on scripts.

Below is an example in a declarative pipeline using sh (shell) with both returnStatus and returnStdout.

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any 
    environment {
        // Using returnStdout
        CC = """${sh(
                returnStdout: true,
                script: 'echo "clang"'
            )}""" 
        // Using returnStatus
        EXIT_STATUS = """${sh(
                returnStatus: true,
                script: 'exit 1'
            )}"""
    }
    stages {
        stage('Example') {
            environment {
                DEBUG_FLAGS = '-g'
            }
            steps {
                sh 'printenv'
            }
        }
    }
}




environment {
    BITBUCKET_COMMON_CREDS = credentials('jenkins-bitbucket-common-creds')
}
this actually sets the following three environment variables:

BITBUCKET_COMMON_CREDS - contains a username and a password separated by a colon in the format username:password.

BITBUCKET_COMMON_CREDS_USR - an additional variable containing the username component only.

BITBUCKET_COMMON_CREDS_PSW - an additional variable containing the password component only.





The following code snippet shows the example Pipeline in its entirety:

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent {
        // Define agent details here
    }
    stages {
        stage('Example stage 1') {
            environment {
                BITBUCKET_COMMON_CREDS = credentials('jenkins-bitbucket-common-creds')
            }
            steps {
                // 
            }
        }
        stage('Example stage 2') {
            steps {
                // 
            }
        }
    }
}



	The following credential environment variables (defined in this Pipeline’s environment directive) are available within this stage’s steps and can be referenced using the syntax:
$BITBUCKET_COMMON_CREDS

$BITBUCKET_COMMON_CREDS_USR

$BITBUCKET_COMMON_CREDS_PSW


Secret files
A secret file is a credential which is stored in a file and uploaded to Jenkins. Secret files are used for credentials that are:

too unwieldy to enter directly into Jenkins, and/or

in binary format, such as a GPG file.


In this example, we use a Kubernetes config file that has been configured as a secret file credential named my-kubeconfig.

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent {
        // Define agent details here
    }
    environment {
        // The MY_KUBECONFIG environment variable will be assigned
        // the value of a temporary file.  For example:
        //   /home/user/.jenkins/workspace/cred_test@tmp/secretFiles/546a5cf3-9b56-4165-a0fd-19e2afe6b31f/kubeconfig.txt
        MY_KUBECONFIG = credentials('my-kubeconfig')
    }
    stages {
        stage('Example stage 1') {
            steps {
                sh("kubectl --kubeconfig $MY_KUBECONFIG get pods")
            }
        }
    }
}


use
withCredentials(

    for ssh sessions


****Combining credentials in one step
Using the Snippet Generator, you can make multiple credentials available within a single withCredentials( …​ ) { …​ } step by doing the following:



*** Parameters

Assuming that a String parameter named "Greeting" has been configuring in the Jenkinsfile, it can access that parameter via ${params.Greeting}:

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    parameters {
        string(name: 'Greeting', defaultValue: 'Hello', description: 'How should I greet the world?')
    }
    stages {
        stage('Example') {
            steps {
                echo "${params.Greeting} World!"
            }
        }
    }
}


(***** Handling failure)

Handling failure
Declarative Pipeline supports robust failure handling by default via its post section which allows declaring a number of different "post conditions" such as: always, unstable, success, failure, and changed. The Pipeline Syntax section provides more detail on how to use the various post conditions.

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'make check'
            }
        }
    }
    post {
        always {
            junit '**/target/*.xml'
        }
        failure {
            mail to: team@example.com, subject: 'The Pipeline failed :('
        }
    }
}


sAn alternative way of handling this, which preserves the early-exit behavior of failures in Pipeline, while still giving junit the chance to capture test reports, is to use a series of try/finally blocks:


Using multiple agents
In all the previous examples, only a single agent has been used. This means Jenkins will allocate an executor wherever one is available, regardless of how it is labeled or configured. Not only can this behavior be overridden, but Pipeline allows utilizing multiple agents in the Jenkins environment from within the same Jenkinsfile, which can helpful for more advanced use-cases such as executing builds/tests across multiple platforms.

In the example below, the "Build" stage will be performed on one agent and the built results will be reused on two subsequent agents, labelled "linux" and "windows" respectively, during the "Test" stage.

Jenkinsfile (Declarative Pipeline)
pipeline {
    agent none
    stages {
        stage('Build') {
            agent any
            steps {
                checkout scm
                sh 'make'
                stash includes: '**/target/*.jar', name: 'app' 
            }
        }
        stage('Test on Linux') {
            agent { 
                label 'linux'
            }
            steps {
                unstash 'app' 
                sh 'make check'
            }
            post {
                always {
                    junit '**/target/*.xml'
                }
            }
        }
        stage('Test on Windows') {
            agent {
                label 'windows'
            }
            steps {
                unstash 'app'
                bat 'make check' 
            }
            post {
                always {
                    junit '**/target/*.xml'
                }
            }
        }
    }
}








