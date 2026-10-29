
pipeline {
    agent any

    stages {
        stage('Check Java Version') {
            steps {
                sh '''
                    echo "Checking Java version..."
                    java -version

                    echo ""
                    echo "Checking Java compiler..."
                    javac -version

                    echo ""
                    echo "Java installation path:"
                    readlink -f "$(which java)"
                '''
            }
        }

        stage('Verify Java 21') {
            steps {
                sh '''
                    JAVA_VERSION=$(java -version 2>&1 | head -n 1)

                    echo "Detected: $JAVA_VERSION"

                    if echo "$JAVA_VERSION" | grep -q '"21'; then
                        echo "SUCCESS: Java 21 is being used by this build."
                    else
                        echo "ERROR: Java 21 is not being used by this build."
                        exit 1
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo 'Java version verification completed successfully.'
        }
        failure {
            echo 'Java version verification failed. Check the console output.'
        }
    }
}
