pipeline {
    agent any

    environment {
        def pythonHome = tool 'Python-3.12'
        // 将 Python 的主目录和 Scripts 目录都加入 PATH
        PATH = "${pythonHome};${pythonHome}\\Scripts;${env.PATH}"
    }

    stages {
        stage('Verify Python Environment') {
            steps {
                bat """
                    @echo off
                    echo "=== 验证Python环境 ==="
                    python --version
                    pip --version
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
        
        // ------------------- 最后的修改在这里 -------------------
        stage('Build') {
            steps {
                bat """
                    @echo off
                    python -m pip install pyinstaller
                    
                    rem 直接调用 pyinstaller.exe 命令，而不是通过 python -m
                    pyinstaller --onefile app.py
                """
            }
        }
        // -----------------------------------------------------------
        
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