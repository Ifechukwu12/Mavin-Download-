pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/Ifechukwu12/Mavin-Download-.git'
            }
        } 
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                 sh 'mvn -f SampleWebApp/pom.xml clean package sonar:sonar'  
                }     
            }
        } 
        stage('Quality Gate') {
            steps {
               waitForQualityGate abortPipeline:true, credentialsId:'Sonar-Cred'
            }
        } 
        stage('Test') {
            steps {
                sh 'cd SampleWebApp && mvn test'
            }
        }
        stage('Build with maven') {
            steps {
                sh 'cd SampleWebApp && mvn package'
            }
        }
        stage('Build to Jfrog') {
            steps {
               rtUpload (
                    serverId: 'my-jfrog',
                    spec: '''{
                          "files": [
                            {
                              "pattern": "**/*.war",
                              "target": "My-repo/"
                            }
                         ]
                    }''',
                ) 
            }
        }
         
        stage('Deploy to tomcat') {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat1', path: '', 
                url: 'http://54.88.237.248:8080')], 
                contextPath: 'default', 
                war: '**/*.war'
            }
        }
        
    }
}
