pipeline {
    agent any

    stages {
        // ===== FRONTEND BUILD =====
        stage('Build Frontend') {
            steps {
                dir('front-crudapp') {
                    sh '''
                    export PATH="/usr/local/bin:$PATH"

                    echo "===== DEBUG: Current Directory ====="
                    pwd
                    echo "===== DEBUG: Files in this folder ====="
                    ls -la
                    echo "===== DEBUG: package.json content ====="
                    cat package.json || echo "No package.json found!"

                    node -v
                    npm -v

                    npm install
                    npm run build
                    '''
                }
            }
        }

        // ===== FRONTEND DEPLOY =====
        stage('Deploy Frontend to Tomcat') {
            steps {
                sh '''
                TOMCAT_DIR="/Users/asishricky/apache-tomcat-9.0.108/webapps/frontapp1"

                if [ -d "$TOMCAT_DIR" ]; then
                    rm -rf "$TOMCAT_DIR"
                fi

                mkdir -p "$TOMCAT_DIR"
                cp -R front-crudapp/dist/* "$TOMCAT_DIR"
                '''
            }
        }

        // ===== BACKEND BUILD =====
        stage('Build Backend') {
            steps {
                dir('cruddemo') {
                    sh '''
                    export PATH="/opt/homebrew/bin:$PATH"
                    mvn clean package
                    '''
                }
            }
        }

        // ===== BACKEND DEPLOY =====
        stage('Deploy Backend to Tomcat') {
            steps {
                sh '''
                TOMCAT_WEBAPPS="/Users/asishricky/apache-tomcat-9.0.108/webapps"
                WAR_FILE="$TOMCAT_WEBAPPS/pipelinebackend.war"
                WAR_DIR="$TOMCAT_WEBAPPS/pipelinebackend"

                if [ -f "$WAR_FILE" ]; then
                    rm -f "$WAR_FILE"
                fi

                if [ -d "$WAR_DIR" ]; then
                    rm -rf "$WAR_DIR"
                fi

                cp cruddemo/target/*.war "$TOMCAT_WEBAPPS/"
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Pipeline Failed.'
        }
    }
}
