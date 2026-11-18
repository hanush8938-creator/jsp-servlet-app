
pipeline {

    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {

        APP_NAME = 'jsp-servlet-app'

        GIT_URL = 'https://github.com/USERNAME/REPOSITORY.git'
        GIT_BRANCH = 'main'

        WAR_FILE = 'target/jsp-servlet-app.war'

        TOMCAT_WEBAPPS = '/opt/tomcat/webapps'

    }

    stages {

        stage('1. Git Checkout') {
            steps {
                echo 'Pulling source code from GitHub...'

                git branch: "${GIT_BRANCH}",
                    url: "${GIT_URL}"
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

        stage('3. Maven Clean') {
            steps {
                sh 'mvn clean'
            }
        }

        stage('4. Build') {
            steps {
                echo 'Building Java application...'

                sh 'mvn compile'
            }
        }

        stage('5. Test') {
            steps {
                echo 'Running test cases...'

                sh 'mvn test'
            }
        }

        stage('6. Package WAR') {
            steps {
                echo 'Creating WAR file...'

                sh 'mvn package -DskipTests'

                sh '''
                    test -f ${WAR_FILE}
                    ls -lh ${WAR_FILE}
                '''
            }
        }

        stage('7. Deploy to Tomcat') {
            steps {
                echo 'Deploying application to Tomcat...'

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
            echo '================================'
            echo 'BUILD SUCCESSFUL'
            echo 'DEPLOYMENT SUCCESSFUL'
            echo '================================'

            echo 'Application URL:'
            echo 'http://YOUR_EC2_PUBLIC_IP:9080/jsp-servlet-app/'
        }

        failure {
            echo 'JENKINS PIPELINE FAILED!'
            echo 'Please check Console Output.'
        }

        always {
            archiveArtifacts(
                artifacts: 'target/*.war',
                fingerprint: true,
                allowEmptyArchive: true
            )
        }
    }
}
