pipeline {
    agent any

    // ------------------- 新的核心改动在这里 -------------------
    environment {
        // 1. 使用内置的 'tool' 函数，获取你在网页上配置的工具路径
        //    'Python-3.12' 必须和你“全局工具配置”里设置的 Name 完全一样
        def pythonHome = tool 'Python-3.12'

        // 2. 将 Python 的路径添加到系统 PATH 环境变量的最前面
        //    在 Windows 上，路径分隔符是分号 (;)
        PATH = "${pythonHome};${env.PATH}"
    }
    // -----------------------------------------------------------

    stages {
        stage('Verify Python Environment') {
            steps {
                // 因为我们已经手动把 Python 的路径加入了 PATH，
                // 现在可以直接调用 python 和 pip 命令了！
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