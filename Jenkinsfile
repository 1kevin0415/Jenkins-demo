pipeline {
    agent any

    // 使用 tools 指令来自动配置环境。
    // 'Python-3.12' 必须和您在 "全局工具配置" -> Python -> "Name" 中设置的完全一样。
    tools {
        python 'Python-3.12'
    }

    stages {
        stage('Verify Python Version') {
            steps {
                // 因为 tools 指令已经把 python 的路径添加到了系统 PATH 中，
                // 所以我们可以在命令行里直接调用 python 和 pip 命令。
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
                // 从代码仓库拉取代码
                // 注意: 如果是私有仓库，需要提前在 Jenkins 中配置好 Git 凭据。
                git url: 'https://github.com/zhangping99/myflaskapp.git', branch: 'main'
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
                // 代码质量检查
                bat """
                    pip install flake8
                    flake8 app.py tests/
                """
            }
        }
        
        stage('Test') {
            steps {
                // 运行单元测试并生成覆盖率报告
                bat """
                    pip install pytest pytest-cov
                    pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                // 无论成功失败，都执行
                always {
                    // 发布HTML格式的测试覆盖率报告
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
                // 将Python应用打包成可执行文件
                bat """
                    pip install pyinstaller
                    pyinstaller --onefile app.py
                """
            }
        }
        
        stage('Deploy') {
            steps {
                // 部署阶段的演示
                // 真实的部署过程会比这复杂，例如：将打包好的文件推送到服务器等。
                echo 'Deploying application...'
            }
        }
    }
    
    // post 块定义了流水线运行结束后需要执行的操作
    post {
        success { 
            echo 'CI/CD pipeline completed successfully!' 
        }
        failure { 
            echo 'CI/CD pipeline failed!' 
        }
    }
}