pipeline {
    agent any
    
    tools {
        maven 'Maven-3.8.5'
        jdk 'JDK-11'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'mvn clean compile'
                echo 'Build completed successfully'
            }
        }
        
        stage('Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('SAST - SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=my-app -Dsonar.projectName=my-app'
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                sh 'mvn clean package'
                script {
                    // Simulation du déploiement - à adapter selon ton environnement
                    if (fileExists('/opt/tomcat/webapps/')) {
                        sh 'cp target/my-app.war /opt/tomcat/webapps/'
                        echo 'Application deployed to Tomcat'
                    } else {
                        echo 'Tomcat directory not found - creating simulated deployment'
                        sh 'mkdir -p simulated-tomcat/webapps/ && cp target/my-app.war simulated-tomcat/webapps/'
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed'
            cleanWs()
        }
        success {
            echo 'Pipeline executed successfully!'
            emailext (
                subject: "SUCCESS: Pipeline ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: "Le pipeline CI/CD s'est exécuté avec succès.\n\nDétails:\n- Job: ${env.JOB_NAME}\n- Build: ${env.BUILD_NUMBER}\n- URL: ${env.BUILD_URL}",
                to: 'votre-email@example.com'
            )
        }
        failure {
            echo 'Pipeline execution failed!'
            emailext (
                subject: "FAILED: Pipeline ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                body: "Le pipeline CI/CD a échoué.\n\nDétails:\n- Job: ${env.JOB_NAME}\n- Build: ${env.BUILD_NUMBER}\n- URL: ${env.BUILD_URL}\n\nVeuillez vérifier les logs.",
                to: 'votre-email@example.com'
            )
        }
    }
}