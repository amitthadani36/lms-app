pipeline {
    agent any

    stages {
        stage('Code quality') {
            steps {
                echo 'SonarQube Analysis'
                sh 'cd webapp && sudo docker run  --rm -e SONAR_HOST_URL="http://13.201.5.250:9000" -e SONAR_LOGIN="sqp_516cb47c0084b4bc79edac23385888d296dc66a4"  -v ".:/usr/src" sonarsource/sonar-scanner-cli:5.0 -Dsonar.projectKey=mainproject'
            }
        }
        stage('Build LMS') {
            steps {
                sh 'cd webapp && npm install && npm run build'
                echo 'LMS build complete'
            }
        }
        stage('Publish artifact') {
            steps {
                script{
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "${packageJSONVersion}"
                    sh "zip webapp/lms-${packageJSONVersion}.zip -r webapp/dist"
                    sh "curl -v -u admin:7326981418 --upload-file webapp/lms-${packageJSONVersion}.zip http://13.201.5.250:8081/repository/lms/"
                }
            }
        }
        stage('Deploy') {
            steps {
                script{
                    def packageJson = readJSON file: 'webapp/package.json'
                    def packageJSONVersion = packageJson.version
                    echo "${packageJSONVersion}"
                    sh "curl -v -u admin:7326981418 -X GET \'http://13.201.5.250:8081/repository/lms/lms-${packageJSONVersion}.zip' --output lms-${packageJSONVersion}.zip"
                    sh "sudo apt instal nginx"
                    sh "sudo rm -rf /var/www/html/*"
                    sh "sudo unzip -o lms-${packageJSONVersion}.zip"
                    sh "sudo cp -r webapp/dist/* /var/www/html/"
            }
         }
        }
        stage('Workspace cleanup') {
            steps {
               echo "Workspace Cleanup"
               cleanWs()

            }

        }
    }
}