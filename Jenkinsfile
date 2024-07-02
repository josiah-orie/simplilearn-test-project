pipeline {
    agent any
    environment{
		dockerHome = tool 'myDocker'
		mavenHome = tool 'myMaven'
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
	}
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "local_maven"
        git 'Default'
        dockerTool 'docker'
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_CREDENTIALS_ID = 'dockerhub'
        DOCKER_IMAGE = 'jossy10/Simplilearn-devops-Project-test'
    }
   // options {
        // Set the timeout for the entire pipeline to avoid long-running jobs
      //  timeout(time: 60, unit: 'MINUTES')
        // Retry the build up to 3 times in case of transient issues
        //retry(3)
  //  }
    stages {
        stage('Check Docker Installation') {
            steps {
                sh 'docker version'
            }
        }
        stage('SCM Checkout'){
            steps{
                checkout poll: false, scm: scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/josiah-orie/simplilearn-test-project.git']])
            }
        }
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                //git 'https://github.com/josiah-orie/simplilearn-test-project.git'

                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
           // }
       // }
        stage('Build Docker Image'){
            steps{
                echo 'Building project docker image  ...'
                script{
                    try{
                        def image = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                    }catch (Exception e){
                        error "Docker build failed: ${e.message}"
                    }
                }
               // withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]){
                //    sh "docker build -t $DOCKER_USER/simplilearn-test-project:test ."
                //}
            }
        }
        stage('Push Docker Image'){
            steps{
                echo 'puching image to docker hub coming in next build ...'
            }
        }
        
    }
    post {
        // If Maven was able to run the tests, even if some of the test
        // failed, record the test results and archive the jar file.
        success {
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts 'target/*.jar'
        }
        failure{
            echo 'Docker image build or Push failed'
        }
    }
}
