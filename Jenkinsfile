pipeline {
    agent any
    environment {
    SONAR_URL = 'https://sonarcloud.io'
    SONAR_TOKEN = credentials('sonar-token')
    SONAR_PROJECT_KEY = 'yesisonlineproject'
    SONAR_ORG = 'yesisonlineorg'
    SNYK_TOKEN = credentials('snyk-token')
  }
  
  stages {    
   stage ('CleanUp Workspace') {
      steps { cleanWs()
     }}
      
  stage('Git Checkout') {
         steps {  git branch: 'main', 
                 url: 'https://github.com/devops-freelancer/webapp.git'
               }}
               
  stage('Trufflehog Scan') {
         steps { sh 'docker run -t trufflesecurity/trufflehog  --json  git https://github.com/devops-freelancer/webapp.git  --max_depth 1  > trufflehog_report.json'
         script {
         def report = readFile 'trufflehog_report.json'
          if (report.contains('DetectorType')) {
                  sh 'echo report'
                  currentBuild.result = 'FAILURE'
                  error "Trufflehog detected potential security issues"
             } else {
                  sh 'echo "Trufflehog not detected potential security issues" > trufflehog_report.json'
            }}}}     
  
stage('Sonar Analysis') {
       steps {	
	          	sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=$SONAR_PROJECT_KEY -Dsonar.organization=$SONAR_ORG -Dsonar.host.url=$SONAR_URL -Dsonar.login=$SONAR_TOKEN'
		        }}
             
stage('Unit Test maven') {
      steps { script {  sh 'mvn verify package'  }}
         }    
    
 stage('Snyk SCA Analysis') {
    steps {		
				withCredentials([string(credentialsId: 'snyk-token', variable: 'snyk-token')]) {
					 sh 'mvn snyk:test -fn'
				}}}

 stage('Wait for quality gate') {
         steps  {
            timeout(time: 1, unit: 'HOURS') {
            script {  def qg = waitForQualityGate()
            if (qg.status != 'OK') {
            error "Pipeline aborted due to quality gate failure: ${qg.status}" }
            }}}
}}}
