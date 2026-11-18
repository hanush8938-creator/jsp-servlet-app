
pipeline {

    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        APP_NAME = 'jsp-servlet-app'
        WAR_FILE = 'target/jsp-servlet-app.war'
        TOMCAT_WEBAPPS = '/opt/tomcat/webapps'
    }

    stages {

        stage('1. Checkout') {
            steps {
                checkout scm
            }
        }

        stage('2. Java and Maven Check') {
            steps {
                sh '''
                    java -version
                    mvn -version
                '''
            }
        }

        stage('3. Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('4. Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('5. Package') {
            steps {
                sh 'mvn package -DskipTests'

                sh '''
                    ls -lh target/
                    test -f ${WAR_FILE}
                '''
            }
        }

        stage('6. Deploy to Tomcat') {
            steps {
                sh '''
                    sudo cp ${WAR_FILE} \
                    ${TOMCAT_WEBAPPS}/${APP_NAME}.war

                    sudo chown tomcat:tomcat \
                    ${TOMCAT_WEBAPPS}/${APP_NAME}.war
                '''
            }
        }
    }

    post {

        success {
            echo 'Application deployed successfully!'
            echo 'http://YOUR_EC2_PUBLIC_IP:9080/jsp-servlet-app/'
        }

        failure {
            echo 'Pipeline failed! Check Console Output.'
        }

        always {
            archiveArtifacts artifacts: 'target/*.war',
                fingerprint: true,
                allowEmptyArchive: true
        }
    }
}

