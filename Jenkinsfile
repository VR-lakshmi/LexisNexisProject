pipeline{
    
    agent any 
    
    stages {
        
        stage('Git Checkout'){
            
            steps{
                
                script{
                    
                    git credentialsId: '', url: 'https://github.com/VR-lakshmi/ProjectLexisNexis.git'
                }
            }
        }
        stage('UNIT testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn test'
                }
            }
        }
        stage('Integration testing'){
            
            steps{
                
                script{
                    
                    sh 'mvn verify -DskipUnitTests'
                }
            }
        }
        stage('Maven build'){
            
            steps{
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('Static code analysis'){
            
            steps{
                
                script{
                    
                    withSonarQubeEnv(credentialsId: 'tokensonar') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                }  
            }
        }
        stage('Quality Gate Status'){
                
            steps{
                script{
                    sleep(60)
                    timeout(time: 2, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                } 
            }
        } 
        stage ('Nexus Upload'){

            steps{

                scripts{
            def mavenPom = readMavenPom file: 'pom.xml'
            def nexusRepoName = mavenPom.version.endWith("SNAPSHOT") ? "project-snapshot" : "project-release"
            nexusArtifactUploader artifacts: [
                    [
                        artifactId: 'webapp', 
                        classifier: '', 
                        file: 'target/${mavenPom.finalname}.war', 
                        type: 'war'
                    ]
                ], 
            credentialsId: 'nexustwo', 
            groupId: 'com.example.maven-project', 
            nexusUrl: '3.8.15.103:8081', 
            nexusVersion: 'nexus3', 
            protocol: 'http',
            repository: nexusRepoName, 
            version: '${mavenPom.version}'
                }
            }
        }
        stage('Ansible Deploy') {
             
            steps {
                scripts{
           ansiblePlaybook credentialsId: 'key', 
           disableHostKeyChecking: true, 
           installation: 'ansibleserver', 
           inventory: '/etc/ansible/hosts', 
           playbook: '/var/lib/jenkins/workspace/test1/main.yml'
                }      
            }   
       
        }
    }
}

