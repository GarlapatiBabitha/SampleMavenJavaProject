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






FROM nginx:alpine
COPY ./usr/share/nginx/html





FROM tomcat:9-jdk21
COPY target/*.war /usr/local/tomcat/webapps/






FROM redis:latest
CMD ["redis-server"]









// calculator.js
function add(a, b) {
    return a + b;
   }
   function subtract(a, b) {
    return a - b;
   }
   function multiply(a, b) {
    return a * b;
   } 
   function divide(a, b) {
    if (b === 0) {
    return "Cannot divide by zero!";
    }
    return a / b;
   }
   // Print the calculated values
   console.log("Addition (2 + 3):", add(2, 3));
   console.log("Subtraction (5 - 2):", subtract(5, 2));
   console.log("Multiplication (4 * 3):", multiply(4, 3));
   console.log("Division (10 / 2):", divide(10, 2)); 



FROM node:16-alpine
WORKDIR /app
COPY calculator.js /app
CMD ["node","calculator.js"]









# version: '3'

services:
  # Database
  db:
    image: mysql:5.7
    volumes:
      - db-data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    networks:
      - wpsite

  # Wordpress
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    ports:
      - '8000:80'
    restart: always
    volumes:
      - /absolute/path/to/your/wordpress/directory:/var/www/html  # Correct local path
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
    networks:
      - wpsite

networks:
  wpsite:

volumes:
  db-data:
