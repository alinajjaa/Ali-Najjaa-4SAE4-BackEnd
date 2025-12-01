pipeline {
    agent any
    
    options {
        timeout(time: 10, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    
    environment {
        PROJECT = 'devopsTD'
        VERSION = '1.0.0'
        MVN_CMD = isUnix() ? './mvnw' : 'mvnw.cmd'
    }
    
    stages {
        // ÉTAPE 1 : Git Checkout
        stage('Git Checkout') {
            steps {
                echo '📥 CLONING CODE...'
                checkout scm
                
                script {
                    if (isUnix()) {
                        sh '''
                            echo "=========== GIT INFO ==========="
                            git log --oneline -1
                            echo "================================"
                        '''
                    } else {
                        bat '''
                            echo =========== GIT INFO ===========
                            git log --oneline -1
                            echo ================================
                        '''
                    }
                }
            }
        }
        
        // ÉTAPE 2 : Environment Setup
        stage('Environment') {
            steps {
                echo '⚙️ SETTING UP...'
                
                script {
                    if (isUnix()) {
                        sh '''
                            echo "Java Version:"
                            java -version
                            echo ""
                            echo "Maven Version:"
                            ./mvnw --version || echo "Maven wrapper not found"
                            echo ""
                            echo "Project Files:"
                            ls -la
                        '''
                    } else {
                        bat '''
                            echo Java Version:
                            java -version
                            echo.
                            echo Maven Version:
                            mvnw.cmd --version || echo Maven wrapper not found
                            echo.
                            echo Project Files:
                            dir
                        '''
                    }
                }
            }
        }
        
        // ÉTAPE 3 : Compilation
        stage('Compile') {
            steps {
                echo '🔨 COMPILING...'
                script {
                    if (isUnix()) {
                        sh './mvnw clean compile'
                    } else {
                        bat 'mvnw.cmd clean compile'
                    }
                }
            }
            
            post {
                success {
                    echo '✅ COMPILATION OK!'
                }
                failure {
                    echo '❌ COMPILATION FAILED!'
                }
            }
        }
        
        // ÉTAPE 4 : Tests
        stage('Tests') {
            steps {
                echo '🧪 TESTING...'
                script {
                    if (isUnix()) {
                        sh './mvnw test'
                    } else {
                        bat 'mvnw.cmd test'
                    }
                }
            }
            
            post {
                always {
                    junit 'target/surefire-reports/**/*.xml'
                }
            }
        }
        
        // ÉTAPE 5 : Package
        stage('Package') {
            steps {
                echo '📦 PACKAGING...'
                script {
                    if (isUnix()) {
                        sh './mvnw package -DskipTests'
                    } else {
                        bat 'mvnw.cmd package -DskipTests'
                    }
                }
            }
        }
        
        // ÉTAPE 6 : Results
        stage('Results') {
            steps {
                echo '📊 SHOWING RESULTS...'
                
                script {
                    if (isUnix()) {
                        sh '''
                            if [ -f target/*.jar ]; then
                                echo "JAR FILES CREATED:"
                                ls -lh target/*.jar
                                echo ""
                                echo "JAR SIZE:"
                                du -h target/*.jar
                            else
                                echo "NO JAR FILES FOUND"
                            fi
                        '''
                    } else {
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
    }
    
    post {
        always {
            echo "🏁 BUILD ${currentBuild.currentResult}"
            echo "URL: ${env.BUILD_URL}"
            
            // Clean workspace pour économiser de l'espace
            cleanWs()
        }
        
        success {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            echo '🎉 SUCCESS! JAR archived.'
        }
        
        failure {
            echo '❌ FAILURE! Check console.'
        }
        
        unstable {
            echo '⚠️ BUILD UNSTABLE!'
        }
    }
}