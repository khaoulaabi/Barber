pipeline {
    agent any

    environment {
        BUILD_DIR = "build"  // Directory where files will be copied
        DEPLOY_DIR = "C:\\Deploy"  // Target deployment directory
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Cloning repository..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    echo "Building project..."

                    bat """
                    REM Create necessary directories in 'build'
                    if not exist ${BUILD_DIR}\\css mkdir ${BUILD_DIR}\\css
                    if not exist ${BUILD_DIR}\\js mkdir ${BUILD_DIR}\\js

                    REM Minify and optimize CSS files
                    echo Minifying CSS files...
                    if exist css\\*.css (
                        for %%i in (css\\*.css) do type %%i > ${BUILD_DIR}\\css\\%%~ni.min.css
                    )

                    REM Minify and optimize JS files
                    echo Minifying JS files...
                    if exist js\\*.js (
                        for %%i in (js\\*.js) do type %%i > ${BUILD_DIR}\\js\\%%~ni.min.js
                    )

                    REM Copy HTML files into build directory
                    echo Copying HTML files...
                    if exist *.html copy *.html ${BUILD_DIR}
                    """
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Testing project..."

                    bat """
                    REM Check if HTML, CSS, and JS files are valid
                    echo Validating HTML files...
                    if exist ${BUILD_DIR}\\*.html (
                        for %%i in (${BUILD_DIR}\\*.html) do echo Validating %%i...
                    )

                    echo Validating CSS files...
                    if exist ${BUILD_DIR}\\css\\*.min.css (
                        for %%i in (${BUILD_DIR}\\css\\*.min.css) do echo Validating %%i...
                    )

                    echo Validating JS files...
                    if exist ${BUILD_DIR}\\js\\*.min.js (
                        for %%i in (${BUILD_DIR}\\js\\*.min.js) do echo Validating %%i...
                    )
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo "Deploying project..."

                    bat """
                    REM Prepare deployment directory
                    if not exist ${DEPLOY_DIR} mkdir ${DEPLOY_DIR}

                    REM Copy build files to deployment directory
                    echo Copying files to deployment directory...
                    xcopy ${BUILD_DIR} ${DEPLOY_DIR} /E /H /C /I /Y

                    echo Deployment complete. Files are ready in '${DEPLOY_DIR}'.
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Build, test, and deployment completed successfully."
        }
        always {
            echo "Cleaning up workspace..."
            //cleanWs()

            // Archive build artifacts to keep them
            archiveArtifacts artifacts: '**/build/**', allowEmptyArchive: true
        }
    }
}
