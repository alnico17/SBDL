pipeline {
    agent any

    environment {
        HADOOP_HOME = "D://hadoop//hadoop-3.3.6"
        PATH = "${env.PATH};D://hadoop//hadoop-3.3.6//bin"
    }

    stages {
        stage('Build') {
            steps {
                sh 'pipenv --python C:/Users/hp/AppData/Local/Programs/Python/Python310/python.exe sync'
            }
        }

        stage('Test') {
            steps {
                sh 'export SPARK_UI_PORT=4050'
                sh 'pipenv run pytest'
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
                bat '''
                if not exist "C:\\Users\\hp\\sbdl-qa" mkdir "C:\\Users\\hp\\sbdl-qa"
                robocopy . "C:\\Users\\hp\\sbdl-qa" sbdl.zip log4j.properties sbdl_main.py sbdl_submit.sh conf /E
                exit 0
                '''
            }
        }

        stage('Deploy') {
            when {
                branch "master"
            }
            steps {
                bat '''
                if not exist "C:\\Users\\hp\\sbdl-prod" mkdir "C:\\Users\\hp\\sbdl-prod"
                robocopy . "C:\\Users\\hp\\sbdl-prod" sbdl.zip log4j.properties sbdl_main.py sbdl_submit.sh conf /E
                exit 0
                '''
            }
        }
    }
}
