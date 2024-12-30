pipeline {
    agent any
    tools {
        maven 'Maven' // Make sure this matches the configured Maven tool in Jenkins
    }
    stages {
        stage('Git Repo & Clean') {
            steps {
                script {
                    // Clean the directory if it already exists
                    if (fileExists('SampleMavenJavaProject')) {
                        bat "rmdir /s /q SampleMavenJavaProject"
                    }
                }
                // Clone the repository
                bat "git clone https://github.com/GarlapatiBabitha/SampleMavenJavaProject.git"
                // Checkout the master branch explicitly
                bat "cd SampleMavenJavaProject && git checkout master"
                // Verify the presence of pom.xml
                script {
                    if (!fileExists('SampleMavenJavaProject/pom.xml')) {
                        error("POM file not found in SampleMavenJavaProject directory!")
                    }
                }
                // Clean the Maven project
                bat "mvn clean -f SampleMavenJavaProject/pom.xml"
            }
        }
        stage('Install') {
            steps {
                // Run Maven install
                bat "mvn install -f SampleMavenJavaProject/pom.xml"
            }
        }
        stage('Test') {
            steps {
                // Run Maven test
                bat "mvn test -f SampleMavenJavaProject/pom.xml"
            }
        }
        stage('Package') {
            steps {
                // Run Maven package
                bat "mvn package -f SampleMavenJavaProject/pom.xml"
            }
        }
    }
}
