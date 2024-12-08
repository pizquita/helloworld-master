pipeline {
    agent any
        environment {
            PATH = "${env.PATH};C:\\Users\\mayca\\AppData\\Local\\Programs\\Python\\Python313\\Scripts;"
        }
    stages {
        stage('Saludo') {
            steps {
                echo 'Prueba ejercicio 1.1'
            }
        }
        stage('Clonar Repositorio') {
            steps {
                //solo para repositorios publicos, no requiere usuario y contraseña
                git 'https://github.com/pizquita/helloworld-master.git'
            }
        }
        stage('Verificar Archivos') {
            steps{
                script{
                    if (isUnix()) {
                        sh 'ls -la'
                    } else {
                        bat 'dir'
                    }
                }
            }
        }
        stage('Mostrar Espacio de Trabajo') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'echo $WORKSPACE'
                    } else {
                        bat 'echo %WORKSPACE%'
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
        stage('UNIT'){
            steps{
                bat '''
                SET PYTHONPATH=%WORKSPACE%
                echo %PATH%
                pytest test\\unit 
                '''
            }
        }
    }
}