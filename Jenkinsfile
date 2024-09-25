pipeline {
    agent any
    
    // Define tools for JDK and Maven
    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }
    
    environment {
        // Define environment variables for Tomcat
        WAR_FILE = 'target/rps.war' // Path to the generated WAR file
        TOMCAT_URL = 'http://localhost:7080' // Tomcat server URL
        TOMCAT_USER = 'war-deployer' // Tomcat Manager username
        TOMCAT_PASSWORD = 'maven-tomcat' // Tomcat Manager password
    }
    
    stages {
        stage('Clean Project') {
            steps {
                // Clean the project using Maven
                bat "mvn clean"
            }
        }

        stage('Build Project') {
            steps {
                // Build the project using Maven to generate the WAR file
                bat "mvn package"
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFilePath = "${WORKSPACE}\\${WAR_FILE}"
                    
                    // Check if the WAR file exists before deploying
                    if (fileExists(warFilePath)) {
                        echo 'WAR file found, proceeding with deployment...'
                        
                        // Deploy the WAR file to Tomcat using curl and Tomcat Manager API
                        bat """
                            curl --upload-file ${warFilePath} \
                            --user ${TOMCAT_USER}:${TOMCAT_PASSWORD} \
                            ${TOMCAT_URL}/manager/text/deploy?path=/rps&update=true
                        """
                    } else {
                        error('WAR file not found! Build might have failed.')
                    }
                }
            }
        }
    }

    post {
        always {
            // Post-build actions
            echo 'Build completed