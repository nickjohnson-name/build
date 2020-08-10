node {  
    stage('Build') { 
        // differential build, leveraging an agreed upon naming convention
        // i.e {ClassName}Test.apex - if all unit tests following
        // this naming convention are picked up in a 
        // runspecified sfdx run test command based on if a unit
        // of code changes, its "related" unit testing should be
        // included and ran as part of the build.
        /*
        delete deploy-sf
        mkdir deploy-sf
        echo 'Current GIT Commit : ${env.GIT_COMMIT}'
        echo 'Previous Known Successful GIT Commit : ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}'
        def newGit = git diff  ${env.GIT_COMMIT} ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}
        for(currentDiff : newGit.line.separatorDelimiter) {
           if(currentDiff != null) {
               if(currentDiff == 'force-app/') {
                   def currentDiffWithoutSRC = currentDiff.trim('force-app/{fileName}')
                   echo 'Current Component : ${currentDiffWithoutSRC}'
                   sh 'cp currentDiffWithoutSRC deploy-sf/'
                   sh 'cp currentDiffWithoutSRC + "-meta.xml"'
                   sh 'cp currentDiffWithoutSRC + "Test"'
                   sh 'cp currentDiffWithoutSRC + "Test-meta.xml"'
                   

               }
           }                 
        }
        */
        echo 'BUILD'

        
    }
    stage('Test') { 
        // 
        echo 'TEST'
    }
    stage('Deploy') { 
        // 
        echo 'Deploy'
    }
}