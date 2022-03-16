// Based on:
// https://raw.githubusercontent.com/redhat-cop/container-pipelines/master/basic-spring-boot/Jenkinsfile

library identifier: "pipeline-library@v1.5",
retriever: modernSCM(
  [
    $class: "GitSCMSource",
    remote: "https://github.com/redhat-cop/pipeline-library.git"
  ]
)

// The name you want to give your Spring Boot application
// Each resource related to your app will be given this name
appName = "hello-java-spring-boot"
env.DEV = "hello-java-spring-boot-dev"
env.BUILD= "hello-java-spring-boot-build"


pipeline {
    // Use the 'maven' Jenkins agent image which is provided with OpenShift 
    agent { label "maven" }
    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }
        stage("Docker Build") {
            steps {
                // This uploads your application's source code and performs a binary build in OpenShift
                // This is a step defined in the shared library (see the top for the URL)
                // (Or you could invoke this step using 'oc' commands!)
                binaryBuild(buildConfigName: appName, buildFromPath: ".")
            }
        }      
       stage('Promote from Build to Dev') {
          steps {
            tagImage(sourceImageName: appName, sourceImagePath: env.BUILD, toImagePath: env.DEV)
           }
        }
       stage ('Verify Deployment to Dev') {
         steps {
            verifyDeployment(projectName: env.DEV, targetApp: appName)
         }
       }

        // You could extend the pipeline by tagging the image,
        // or deploying it to a production environment, etc......
    }
}
