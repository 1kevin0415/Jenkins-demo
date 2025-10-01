pipeline {
    agent any

    environment {
        def pythonHome = tool 'Python-3.12'
        PATH = "${pythonHome};${pythonHome}\\Scripts;${env.PATH}"
    }

    stages {
        stage('Verify Python Environment') {
            steps {
                bat """
                    @echo off
                    echo "=== 验证Python环境 ==="
                    python --version
                    python -m pip --version
                    where python
                    echo "Python环境验证通过！"
                """
            }
        }
        
        stage('Checkout') {
            steps {
                git url: 'https://github.com/1kevin0415/Jenkins-demo', branch: 'main'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                bat """
                    @echo off
                    echo "=== 安装项目依赖 ==="
                    python -m pip install --upgrade pip
                    python -m pip install -r requirements.txt
                """
            }
        }
        
        stage('Lint') {
            steps {
                bat """
                    python -m pip install flake8
                    python -m flake8 app.py tests/
                """
            }
        }
        
        stage('Test') {
            steps {
                bat """
                    python -m pip install pytest pytest-cov
                    python -m pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }
        
        stage('Build') {
            steps {
                bat """
                    python -m pip install pyinstaller
                    python -m pyinstaller --onefile app.py
                """
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
    
    post {
        success { 
            echo 'CI/CD pipeline completed successfully!' 
        }
        failure { 
            echo 'CI/CD pipeline failed!' 
        }
    }
}