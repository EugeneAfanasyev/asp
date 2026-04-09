def PLATFORM_PATH = 'C:\\Program Files\\1Cv8\\8.3.27.1989\\bin\\1cv8.exe'
def FLAGS = '/DisableStartupMessages /DisableStartupDialogs /DisableUnrecoverableErrorMessage'

pipeline {
    options {
        timestamps()
        timeout(time: 60, unit: 'MINUTES')
    }
    agent any
    environment {
        LOG = "${env.WORKSPACE}\\build.log"
    }
    post {
        always {
            bat "if exist \"${env.LOG}\" type \"${env.LOG}\""
            allure includeProperties: false, jdk: '', resultPolicy: 'LEAVE_AS_IS', results: [[path: 'allure-results']]
            junit allowEmptyResults: true, testResults: 'junit.xml'
        }
    }
    stages {
        stage('Сборка') {
            steps {
                bat "\"${PLATFORM_PATH}\" CREATEINFOBASE \"File=${WORKSPACE}\" /Out \"${LOG}\" -NoTruncate"
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /LoadCfg \"${params.cf_path}\" ${FLAGS} /Out \"${LOG}\" -NoTruncate"
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /UpdateDBCfg ${FLAGS} /Out \"${LOG}\" -NoTruncate"
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /LoadCfg \"${params.cfe_path}\" -Extension \"YAXUNIT\" ${FLAGS} /Out \"${LOG}\" -NoTruncate"
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /UpdateDBCfg -Extension \"YAXUNIT\" ${FLAGS} /Out \"${LOG}\" -NoTruncate"
            }
        }
        stage('Инициализация') {
            steps {
                // Первоначальный запуск с ключом YAXUNIT_EXTENSION_INIT
                bat "\"${PLATFORM_PATH}\" ENTERPRISE /F\"${WORKSPACE}\" /C\"YAXUNIT_EXTENSION_INIT\" ${FLAGS} /Out \"${LOG}\" -NoTruncate"
            }
        }
        stage('Тестирование') {
            steps {
                // Запуск тестов; YAXUnit формирует junit.xml и allure-results согласно json_path
                bat "\"${PLATFORM_PATH}\" ENTERPRISE /F\"${WORKSPACE}\" /C\"RunUnitTests=${params.json_path};\" ${FLAGS} /Out \"${LOG}\" -NoTruncate"
            }
        }
    }
}
