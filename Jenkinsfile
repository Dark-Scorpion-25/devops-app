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
                script {
                    // Analyse SonarQube (optionnel si pas installé)
                    try {
                        withSonarQubeEnv('sonar-server') {
                            sh 'mvn sonar:sonar -Dsonar.projectKey=my-app -Dsonar.projectName=my-app'
                        }
                    } catch (Exception e) {
                        echo "SonarQube non disponible: ${e.getMessage()}"
                        echo "Continuing without SAST..."
                    }
                }
            }
        }
        
        stage('Package & Deploy') {
            steps {
                sh 'mvn clean package'
                script {
                    // Simulation de déploiement
                    echo "Fichier WAR généré: target/my-app.war"
                    echo "Taille: ${sh(script: 'du -h target/my-app.war | cut -f1', returnStdout: true).trim()}"
                    
                    // Pour un vrai déploiement, décommente les lignes ci-dessous:
                    // sh 'sudo systemctl stop tomcat9'
                    // sh 'cp target/my-app.war /var/lib/tomcat9/webapps/'
                    // sh 'sudo systemctl start tomcat9'
                    // echo "Application déployée sur Tomcat"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed'
            archiveArtifacts artifacts: 'target/*.war', fingerprint: true
        }
        success {
            echo '✅ Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline execution failed!'
        }
    }
}