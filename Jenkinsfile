pipeline {
    agent any

    // 使用 tool 指令，并明确指定您在全局工具配置中设置的 Name 和 Jenkins 识别的 Type
    tools {
        tool name: 'Python-3.12', type: 'jenkins.plugins.shiningpanda.tools.PythonInstallation'
    }

    stages {
        stage('Verify Python Version') {
            steps {
                // 现在，Jenkins 应该能正确地找到 Python 并将其加入 PATH
                bat """
                    @echo off
                    echo "=== 验证Python环境 ==="
                    python --version
                    pip --version
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
                    pip install -r requirements.txt
                """
            }
        }
        
        stage('Lint') {
            steps {
                bat """
                    pip install flake8
                    flake8 app.py tests/
                """
            }
        }
        
        stage('Test') {
            steps {
                bat """
                    pip install pytest pytest-cov
                    pytest --cov=app tests/ --cov-report=html
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
                    pip install pyinstaller
                    pyinstaller --onefile app.py
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