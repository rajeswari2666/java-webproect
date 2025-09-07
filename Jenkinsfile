pipeline {
    agent any
    tools {
        maven 'Maven 3.8.9'
    }
    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-credentials')
        AWS_SECRET_ACCESS_KEY = credentials('aws-credentials')
        VERSION_NUMBER = 0
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
                    sh "aws s3 cp boxfuse-sample-java-war-hello/target/*.war s3://elasticbeanstalk-us-east-1-374468783315/"
                    sh "aws elasticbeanstalk create-application-version --application-name mywebapp --version-label v${VERSION_NUMBER} --source-bundle S3Bucket=\"elasticbeanstalk-us-east-1-374468783315\",S3Key=\"*.war\""
                    sh "aws elasticbeanstalk update-environment --application-name mywebapp --environment-name Mywebapp-env --version-label v${VERSION_NUMBER}"
                }
            }
        }
        
    }
}
