# Créer le Jenkinsfile complet
@"
pipeline {
    agent any
    
    options {
        timeout(time: 10, unit: 'MINUTES')
    }
    
    environment {
        PROJECT = 'devopsTD'
        VERSION = '1.0.0'
    }
    
    stages {
        // ÉTAPE 1 : Git Checkout
        stage('Git Checkout') {
            steps {
                echo '📥 CLONING CODE...'
                checkout scm
                
                script {
                    bat '''
                        echo =========== GIT INFO ===========
                        git log --oneline -1
                        echo ================================
                    '''
                }
            }
        }
        
        // ÉTAPE 2 : Environment Setup
        stage('Environment') {
            steps {
                echo '⚙️ SETTING UP...'
                
                bat '''
                    echo Java Version:
                    java -version
                    echo.
                    echo Maven Version:
                    mvnw --version
                    echo.
                    echo Project Files:
                    dir
                '''
            }
        }
        
        // ÉTAPE 3 : Compilation
        stage('Compile') {
            steps {
                echo '🔨 COMPILING...'
                bat 'mvnw clean compile'
            }
            
            post {
                success {
                    echo '✅ COMPILATION OK!'
                }
                failure {
                    error '❌ COMPILATION FAILED!'
                }
            }
        }
        
        // ÉTAPE 4 : Tests
        stage('Tests') {
            steps {
                echo '🧪 TESTING...'
                bat 'mvnw test'
            }
            
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        // ÉTAPE 5 : Package
        stage('Package') {
            steps {
                echo '📦 PACKAGING...'
                bat 'mvnw package -DskipTests'
            }
        }
        
        // ÉTAPE 6 : Results
        stage('Results') {
            steps {
                echo '📊 SHOWING RESULTS...'
                
                script {
                    bat '''
                        if exist target\\*.jar (
                            echo JAR FILES CREATED:
                            dir target\\*.jar
                            echo.
                            echo JAR SIZE:
                            for %%i in (target\\*.jar) do echo %%~zi bytes
                        ) else (
                            echo NO JAR FILES FOUND
                        )
                    '''
                }
            }
        }
    }
    
    post {
        always {
            echo "🏁 BUILD \${currentBuild.currentResult}"
            echo "URL: \${env.BUILD_URL}"
        }
        
        success {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            echo '🎉 SUCCESS! JAR archived.'
        }
        
        failure {
            echo '❌ FAILURE! Check console.'
        }
    }
}
"@ | Out-File -FilePath Jenkinsfile -Encoding UTF8