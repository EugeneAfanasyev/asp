def PLATFORM_PATH = 'C:\\Program Files\\1Cv8\\8.3.27.1989\\bin\\1cv8.exe'

pipeline {
    options {
        timestamps()
        timeout(time: 60, unit: 'MINUTES')
    }
    agent any
    post {
        always {
            allure includeProperties: false, jdk: '', resultPolicy: 'LEAVE_AS_IS', results: [[path: 'allure-results']]
            junit allowEmptyResults: true, testResults: 'junit.xml'
        }
    }
    stages {
        stage('Сборка') {
            steps {
                // 1. Создаем пустую Информационную Базу (BASE_PATH = каталог сборки)
                bat "\"${PLATFORM_PATH}\" CREATEINFOBASE \"File=${WORKSPACE}\""

                // 2. Загружаем конфигурацию CF
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /LoadCfg \"${params.cf_path}\" /DisableStartupMessages /DisableStartupDialogs"
                // Обновляем конфигурацию БД
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /UpdateDBCfg /DisableStartupMessages /DisableStartupDialogs"

                // 3. Загружаем расширение CFE
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /LoadCfg \"${params.cfe_path}\" /Extension \"YAXUNIT\" /DisableStartupMessages /DisableStartupDialogs"
                // Обновляем конфигурацию БД для расширения
                bat "\"${PLATFORM_PATH}\" DESIGNER /F\"${WORKSPACE}\" /UpdateDBCfg /Extension \"YAXUNIT\" /DisableStartupMessages /DisableStartupDialogs"
            }
        }
        stage('Инициализация') {
            steps {
                // 4. Первоначальный запуск с ключом YAXUNIT_EXTENSION_INIT
                bat "\"${PLATFORM_PATH}\" ENTERPRISE /F\"${WORKSPACE}\" /C\"YAXUNIT_EXTENSION_INIT\" /DisableStartupMessages /DisableStartupDialogs /DisableUnrecoverableErrorMessage"
            }
        }
        stage('Тестирование') {
            steps {
                // 5. Запуск тестов; YAXUnit формирует junit.xml и allure-results согласно json_path
                bat "\"${PLATFORM_PATH}\" ENTERPRISE /F\"${WORKSPACE}\" /C\"RunUnitTests=${params.json_path};\" /DisableStartupMessages /DisableStartupDialogs /DisableUnrecoverableErrorMessage"
            }
        }
    }
}
