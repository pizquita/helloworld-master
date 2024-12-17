pipeline {
    agent none
    options {
        skipDefaultCheckout true
    }
    stages {
        stage('Checkout') {
            agent { label 'ARepo' }
            steps {
                bat 'whoami'                     // Usuario que ejecuta la etapa
                bat 'hostname'                   // Nombre del nodo
                bat 'echo %WORKSPACE%'           // Directorio de trabajo actual
                git 'https://github.com/pizquita/helloworld-master.git'
                bat '''
                    dir
                    echo %WORKSPACE%
                '''
                stash includes: '**', name: 'source-code'
            }
        }
        stage('Build') {
            agent { label 'ARepo' }
            steps {
                unstash 'source-code'
                bat 'echo Construye'
            }
        }
        stage('Unit Tests') {
            agent { label 'AUnit' }
            steps {
                bat 'whoami'
                bat 'hostname'
                bat 'echo %WORKSPACE%'
                unstash 'source-code'
                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                   bat '''
                        SET PYTHONPATH=%WORKSPACE%
                        pytest test\\unit --junitxml=result-unit.xml
                    '''
                }
                stash includes: 'result-unit.xml', name: 'unit-test-results' // Almacena el archivo de pruebas
            }
        }
        stage('REST Tests') {
            agent { label 'ARest' }
            steps {
                bat 'whoami'
                bat 'hostname'
                bat 'echo %WORKSPACE%'
                unstash 'source-code'

                catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                    bat '''
                        SET FLASK_APP=app\\api.py
                        SET PYTHONPATH=%WORKSPACE%
                        start flask run
                        SET JAVA_HOME=C:\\Program Files\\Java\\jdk-21
                        SET PATH=%JAVA_HOME%\\bin;%PATH%
                        start java -jar "C:\\Master DevOps\\Ejercicios\\Modulo4\\helloworld-master\\test\\wiremock\\wiremock-standalone-3.10.0.jar" --port 9090 --root-dir "C:\\Master DevOps\\Ejercicios\\Modulo4\\helloworld-master\\test\\wiremock"
                        pytest --junitxml=result-rest.xml test\\rest
                    '''
                }
                stash includes: 'result-rest.xml', name: 'rest-test-results'
            }
        }
        stage('Publish Results') {
            agent { label 'ARepo' }
            steps {
                unstash 'unit-test-results'       // Recupera resultados de pruebas unitarias
                unstash 'rest-test-results'       // Recupera resultados de pruebas REST
                junit 'result*.xml'
            }
        }
    }
    post {
        always {
            script {
                // Limpia los workspaces de todos los agentes utilizados
                def nodes = ['ARepo', 'AUnit', 'ARest'] // Lista de nodos utilizados
                nodes.each { nodeLabel ->
                    node(nodeLabel) {
                        cleanWs()
                    }
                }
            }
        }
    }
}
