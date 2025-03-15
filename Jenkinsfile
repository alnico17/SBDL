pipeline {
    agent any

    environment {
        HADOOP_HOME = "D:\\hadoop\\hadoop-3.3.6"
        PATH = "${env.PATH};D:\\hadoop\\hadoop-3.3.6\\bin"
    }

    stages {
        stage('Build') {
            steps {
                bat 'pipenv --python C:\\Users\\hp\\AppData\\Local\\Programs\\Python\\Python310\\python.exe sync'
            }
        }

        stage('Test') {
            steps {
                bat 'set SPARK_UI_PORT=4050'
                bat 'pipenv run pytest'
            }
        }

        stage('Package') {
            when {
                anyOf { branch "master"; branch "release" }
            }
            steps {
                script {
                    def os = isUnix() ? 'linux' : 'windows'
                    if (os == 'windows') {
                        bat 'powershell Compress-Archive -Path lib -DestinationPath sbdl.zip'
                    } else {
                        sh 'zip -r sbdl.zip lib'
                    }
                }
            }
        }

        stage('Release') {
            when {
                branch "release"
            }
            steps {
                script {
                    def releasePath = "C:\\Users\\hp\\sbdl-qa"
                    bat "mkdir \"${releasePath}\""
                    bat "xcopy /E /I sbdl.zip log4j.properties sbdl_main.py sbdl_submit.sh conf \"${releasePath}\""
                }
            }
        }

        stage('Deploy') {
            when {
                branch "master"
            }
            steps {
                script {
                    def deployPath = "C:\\Users\\hp\\sbdl-prod"
                    bat "mkdir \"${deployPath}\""
                    bat "xcopy /E /I sbdl.zip log4j.properties sbdl_main.py sbdl_submit.sh conf \"${deployPath}\""
                }
            }
        }
    }
}
