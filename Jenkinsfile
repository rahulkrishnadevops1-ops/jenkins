pipeline {
    agent { label 'tomcat-agent' }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/rahulkrishnadevops1-ops/jenkins.git'
            }
        }

        stage('Build') {
            when { branch 'main' }   // only on main
            steps {
                dir('javaapp-standalone') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir('javaapp-standalone') {
                    sh 'mvn test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-server') {
                    dir('javaapp-standalone') {
                        sh '''
                        mvn sonar:sonar \
                          -Dsonar.projectKey=java-sample-21 \
                          -Dsonar.host.url=$SONAR_HOST_URL \
                          -Dsonar.login=$SONAR_AUTH_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            when { branch 'main' }   // only on main
            steps {
                dir('javaapp-standalone/target') {
                    sh 'JENKINS_NODE_COOKIE=dontKillMe nohup java -jar *.jar > app.log 2>&1 &'
                }
                echo "App deployed on main branch ✅"
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully for branch: ${env.BRANCH_NAME}"
        }
        failure {
            echo "Pipeline failed for branch: ${env.BRANCH_NAME}"
        }
    }
} 
