pipeline {
  agent {
    docker {
      image 'silverpeas/silverbuild'
      args '-v $HOME/.m2:/home/silverbuild/.m2 -v $HOME/.gitconfig:/home/silverbuild/.gitconfig -v $HOME/.ssh:/home/silverbuild/.ssh -v $HOME/.gnupg:/home/silverbuild/.gnupg'
    }
  }
  stages {
    stage('Publish project web site') {
      steps {
        script {
          // publish the project web site only for the master branch
          git credentialsId: 'cacc0467-7c85-41d1-bf4e-eaa470dd5e59', branch: 'master', poll: false, url: "https://github.com/Silverpeas/Silverpeas-Project-Web-Site"
          def status = sh script: """
mvn clean site-deploy
""", returnStatus: true
          if (status != 0) {
            emailext(
                subject: "Silverpeas Web Site publishing failure!",
                body: """
I was unable to publish the Silverpeas Community Web Site to www.silverpeas.org!
Please, consult the console log of the build ${currentBuild.displayName} of ${currentBuild.projectName}
""",
                to: 'miguel.moquillon@silverpeas.org, yohann.chastagnier@silverpeas.org'
            )
          }
        }
      }
    }
  }

  post {
    always {
      step([$class                  : 'Mailer',
            notifyEveryUnstableBuild: true,
            recipients              : "miguel.moquillon@silverpeas.org, yohann.chastagnier@silverpeas.org, david.lesimple@silverpeas.org",
            sendToIndividuals       : true])
    }
  }
}