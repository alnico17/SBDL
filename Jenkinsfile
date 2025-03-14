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
                branch 'release'
            }
            steps {
                sh '''
                scp -i /home/prashant/cred/edge-node_key.pem -o "StrictHostKeyChecking no" \
                -r sbdl.zip log4j.properties sbdl_main.py sbdl_submit.sh conf \
                prashant@40.117.123.105:/home/prashant/sbdl-qa
                '''
            }
        }

        stage('Deploy') {
            when {
                branch '
