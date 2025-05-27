pipeline {
    agent any

    tools {
        maven 'Maven3' //
    }

    stages {
        stage('Clone') {
            steps {
                cleanWs()
                // !!! CAMBIA LA URL A TU REPOSITORIO Y ASEGÚRATE QUE LA RAMA (branch) SEA CORRECTA ('master' o 'main') !!!
                git branch: 'master', url: 'https://github.com/Hoshe963/examen2-despliegue.git'
            }
        }

        stage('Build') {
            steps {
                // !!! RUTA AL POM.XML DENTRO DE LA CARPETA 'micro-product' !!!
                sh 'mvn -f micro-product/pom.xml clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                // !!! RUTA AL POM.XML DENTRO DE LA CARPETA 'micro-product' !!!
                sh 'mvn -f micro-product/pom.xml test'
            }
        }

        stage('Sonar Analysis') {
            steps {
                // !!! USA EL NOMBRE DE TU SERVIDOR SONARQUBE CONFIGURADO EN JENKINS (Configure System) !!!
                withSonarQubeEnv('SonarQubeLocal') {
                    // !!! RUTA AL POM.XML Y MUY IMPORTANTE: -Dsonar.projectKey !!!
                    // !!! REEMPLAZA 'TU_GROUP_ID:TU_ARTIFACT_ID' CON LOS VALORES DE TU pom.xml !!!
                    // Ejemplo: si tu groupId es 'com.example' y artifactId es 'my-app', usa 'com.example:my-app'
                    sh 'mvn -f micro-product/pom.xml sonar:sonar -Dsonar.projectKey=TU_GROUP_ID:TU_ARTIFACT_ID'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 10, unit: 'MINUTES') { // Espera máximo 10 minutos
                    // !!! USA EL NOMBRE DE TU SERVIDOR SONARQUBE CONFIGURADO EN JENKINS !!!
                    waitForQualityGate abortPipeline: true, sonarqubeInstallationName: 'SonarQubeLocal'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline ejecutado correctamente.'
        }
        failure {
            echo '❌ Falló el pipeline.'
        }
    }
}
