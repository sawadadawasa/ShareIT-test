pipeline {
    agent any
    tools {
        maven "Maven 3.6.1"
        jdk "JDK 8 Update 9.0.4"
    }
    stages {
        stage('チェックアウト') {
            steps {
                // Gitリポジトリの設定
                git url: 'https://github.com/mimaken3/Share-IT.git'
            }
        }
        stage('Mavenビルド') {
            steps {
                slackSend (color: '#00FFFF', message: "テストを開始します: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
                sh "mvn clean package"
            }
        }
        stage('テスト結果の集計') {
            steps {
                // JUnitテスト結果の集計
                junit('target/surefire-reports/*.xml')
                // JaCoCo結果の集計
                jacoco(execPattern: 'target/jacoco.exec')
            }
        }
        stage('コード解析結果の集計') {
            steps {
                // CheckStyle結果の集計
                checkstyle(pattern: 'target/checkstyle-result.xml')
                // FindBugs結果の集計
                findbugs(pattern: 'target/findbugsXml.xml')
            }
        }
        stage('成果物の保存'){
            steps {
                  archiveArtifacts 'target/*.war'
            }
        }
    }
    post {
        success {
                slackSend (color: '#00FFFF', message: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        // デプロイ
            // sh 'rsync -e "ssh -i /var/jenkins_home/.ssh/AWSkeypair-mimataa.pem" -avzr --delete /var/jenkins_home/workspace/pipeline-project/target/project.war  ec2-user@54.250.246.28:/home/ec2-user/docker-ec2/tomcat_home/'
        }
        unstable {
           slackSend color: '#00FFFF',
                message: "Unstable: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
        
    }
        failure {
                slackSend color: '#FF4500',
                message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
            
        } 
      }
}