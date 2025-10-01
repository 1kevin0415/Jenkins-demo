pipeline {
    agent any

    // 使用 Jenkins 识别的完整类型名作为 tool 指令
    tools {
        jenkins.plugins.shiningpanda.tools.PythonInstallation 'Python-3.12'
    }

    stages {
        stage('Verify Python Version') {
            steps {
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
                // 这个阶段其实可以省略，因为 Jenkins 已经在一开始就拉取了代码来读取 Jenkinsfile
                // 但保留它也没有坏处，可以确保工作区是最新的
                git url: 'https://github.com/1kevin0415/Jenkins-demo', branch: 'main'
            }
        }
        
        // ... 后续其他 stage ...
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