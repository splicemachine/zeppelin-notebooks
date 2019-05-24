node('python') {

  // run tests
  try {

    // Mark the code checkout 'stage'....
    stage('Checkout') {
      // Checkout code from repository
      checkout scm
    }

      stage('Prep') {
        // install gem dependencies
        sh 'pip3 install -r requirements.txt'
     }

      stage('Lint') {
        int status = sh(script: 'python3 apply_paragraph_standards.py -t Repair -l', returnStatus: true)

        if (status != 0) {
            error("Please run 'python apply_paragraph_standards.py -t Repair -m' on your local branch to reset note.json files.")
            currentBuild.result = "FAILURE"
        } else {
            currentBuild.result = "SUCCESS"
        }
      }


      stage('Test') {
        if (env.BRANCH_NAME != 'master') {
          sh 'echo "not master"'
        }
      }

      stage('Apply') {
        if (env.BRANCH_NAME == 'master') {
          sh 'echo "master"'
        }
      }

  } catch (any) {

    // if there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw any

  } finally {

    // success or failure, always send notifications
    setBuildStatus("Build status", currentBuild.result);

  }
}

void setBuildStatus(String message, String state) {
  step([
      $class: "GitHubCommitStatusSetter",
      reposSource: [$class: "ManuallyEnteredRepositorySource", url: "https://github.com/splicemachine/zeppelin-notebooks"],
      contextSource: [$class: "ManuallyEnteredCommitContextSource", context: "ci/jenkins/build-status"],
      errorHandlers: [[$class: "ChangingBuildStatusErrorHandler", result: "UNSTABLE"]],
      statusResultSource: [ $class: "ConditionalStatusResultSource", results: [[$class: "AnyBuildResult", message: message, state: state]] ]
  ]);
}

