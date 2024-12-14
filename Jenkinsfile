pipeline {
    agent any
        environment {
            PATH = "${env.PATH};C:\\Users\\mayca\\AppData\\Local\\Programs\\Python\\Python313\\Scripts;"
        }
    stages {
        stage('Get Repository') {
            steps {
                echo 'Prueba ejercicio 1.1'
                //solo para repositorios publicos, no requiere usuario y contraseña
                git 'https://github.com/pizquita/helloworld-master.git'
                script{
                    if (isUnix()) {
                        sh 'ls -la'
                        sh 'echo $WORKSPACE'
                    } else {
                        bat '''
                        dir
                        echo %WORKSPACE%
                        '''
                    }
                }
            }
        }
        stage('Build'){
            steps{
                script {
                    if (isUnix()) {
                        sh 'echo Construye'
                    } else {
                        bat 'echo Construye'
                    }
                }
            }
        }
        stage('test'){
            parallel{
                 stage('UNIT'){
                    steps{
                        bat '''
                        SET PYTHONPATH=%WORKSPACE%
                        pytest --junitxml=result-unit.xml test\\unit
                        '''
                    }
                }
                stage('REST'){
                    steps{
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
                }
            }
        }
       stage('Results'){
           steps{
               junit 'result*.xml'
           }
       }
    }
}