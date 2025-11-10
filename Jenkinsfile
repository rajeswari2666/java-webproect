pipeline {
    agent any
    tools {
        maven 'Maven 3.8.9'
    }
    environment {
        VERSION_NUMBER = 0.6
    }
    stages {
        stage('Build') {
            steps {
                sh "mvn -f boxfuse-sample-java-war-hello/pom.xml -B -DskipTests clean package"
            }
            post {
                success {
                    echo "Now Archiving the Artifacts....."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage('Deploy') {
            steps {
                withAWS(credentials: 'aws-credentials', region: 'us-east-1') {
                    sh "aws s3 cp boxfuse-sample-java-war-hello/target/*.war s3://elasticbeanstalk-us-east-1-695090997008/"
                    sh "aws elasticbeanstalk create-application-version --application-name Webapplication --version-label v${VERSION_NUMBER} --source-bundle S3Bucket=\"elasticbeanstalk-us-east-1-695090997008\",S3Key=\"hello-1.0.war\""
                    sh "aws elasticbeanstalk update-environment --application-name Webapplication --environment-name Webapplication-env --version-label v${VERSION_NUMBER}"
                }
            }
        }
    }
}
