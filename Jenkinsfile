pipeline {
    // agent {
        // docker {
            // image 'maven:3-alpine'
            // args '-v /root/.m2:/root/.m2'
        //}
    // }
	agent any
	 
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/AshokSingh10/simple-java-maven-app.git'
            }
            
        }
        
        stage('Build') {
            
            steps {
                notify('Build')
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                  // If it fails the pipeline continues
                   sh 'mvn -B -DskipTests clean package'
              }
            }
            
        }
        stage('Test') { 
            steps {
                sh 'mvn test' 
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
        }
        
        stage('Archival')
        {
            steps
            {
               publishHTML(target: [allowMissing: true, 
                     alwaysLinkToLastBuild: false, 
                          keepAll: true, 
                          reportDir: 'target/site/jacoco/', 
                         reportFiles: 'index.html', 
                        reportName: 'Code Coverage'])
    
               step([$class: 'ArtifactArchiver', 
               artifacts: "target/*.?ar", 
               excludes: null])
            }
           
        }
           
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
       
    }
          
}

def notify(status){
    emailext (
      to: "pbdemos@gmail.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}
