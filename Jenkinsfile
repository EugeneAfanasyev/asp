def PLATFORM_PATH = 'C:\\Program Files\\1Cv8\\8.3.27.1989\\bin\\1cv8.exe'
def FLAGS = '/DisableStartupMessages /DisableStartupDialogs /DisableUnrecoverableErrorMessage /Out build.log -NoTruncate'

pipeline {
    options {
        timestamps()
        timeout(time: 60, unit: 'MINUTES')
    }
    agent any
    post {
        always {
            bat "if exist build.log type build.log"
            allure includeProperties: false, jdk: '', resultPolicy: 'LEAVE_AS_IS', results: [[path: 'allure-results']]
            junit allowEmptyResults: true, testResults: 'report.xml'
        }
    }
    stages {
        stage('Сборка') {
            steps {
                bat "\"${PLATFORM_PATH}\" CREATEINFOBASE \"File=${WORKSPACE}\" ${FLAGS}"
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /LoadCfg \"${params.cf_path}\" ${FLAGS}"
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /UpdateDBCfg ${FLAGS}"
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /LoadCfg \"${params.cfe_path}\" -Extension \"YAXUNIT\" ${FLAGS}"
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /UpdateDBCfg -Extension \"YAXUNIT\" ${FLAGS}"
            }
        }
        stage('Инициализация') {
            steps {
                bat "\"${PLATFORM_PATH}\" ENTERPRISE /F\"${WORKSPACE}\" /C\"YAXUNIT_EXTENSION_INIT\" ${FLAGS}"
            }
        }
        stage('Тестирование') {
            steps {
                bat "\"${PLATFORM_PATH}\" ENTERPRISE /F\"${WORKSPACE}\" /C\"RunUnitTests=${params.json_path};\" ${FLAGS}"
            }
        }
    }
}
