pipeline {
    agent {
        label 'docker'
    }
    
    stages {
        stage('Source') {
            steps {
                git 'https://github.com/Eschinlle/unir-cicd'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building stage!'
                sh 'make build'
            }
        }
        
        stage('Unit tests') {
            steps {
                echo 'Ejecutando pruebas unitarias...'
                sh 'make test-unit'
                archiveArtifacts artifacts: 'results/*.xml', allowEmptyArchive: true
            }
        }
        
        stage('API tests') {
            steps {
                echo 'Ejecutando pruebas de API...'
                sh 'make test-api'
                archiveArtifacts artifacts: 'results/*api*.xml', allowEmptyArchive: true
            }
        }
        
        stage('E2E tests') {
            steps {
                echo 'Ejecutando pruebas End-to-End...'
                sh 'make test-e2e'
                archiveArtifacts artifacts: 'results/*e2e*.xml', allowEmptyArchive: true
            }
        }
    }
    
    post {
        always {
            junit testResults: 'results/*_result.xml', allowEmptyResults: true
            
            script {
                if (fileExists('results/*api*_result.xml')) {
                    echo 'Informes de API tests disponibles en los artefactos'
                }
                if (fileExists('results/*e2e*_result.xml')) {
                    echo 'Informes de E2E tests disponibles en los artefactos'
                }
            }
            
            cleanWs()
        }
        
        failure {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def buildUrl = env.BUILD_URL
                
                def emailSubject = "❌ Pipeline Failed: ${jobName} - Build #${buildNumber}"
                
                def emailBody = """
Hola,

El pipeline de Jenkins ha fallado:

📋 Detalles del trabajo:
- Nombre del trabajo: ${jobName}
- Número de ejecución: ${buildNumber}
- URL del build: ${buildUrl}
- Fecha y hora: ${new Date()}

🔍 Por favor, revisa los logs del build para más detalles sobre el fallo.

Saludos,
Jenkins CI/CD System
                """.stripIndent()
                
                echo "=== CONTENIDO DEL EMAIL DE NOTIFICACIÓN ==="
                echo "Para: chinllesteven8@gmail.com"
                echo "Asunto: ${emailSubject}"
                echo "Cuerpo del mensaje:"
                echo "${emailBody}"
                echo "==============================================="
                
                /*
                emailext (
                    subject: emailSubject,
                    body: emailBody,
                    to: 'chihnllesteven8@gmail.com',
                    mimeType: 'text/plain'
                )
                */
                
                echo "📧 Correo de notificación preparado para envío automático"
                echo "🔧 Trabajo: ${jobName} | Build: #${buildNumber} | Estado: FALLIDO"
            }
        }
        
        success {
            echo '✅ Pipeline completado exitosamente!'
            echo "🎉 Trabajo: ${env.JOB_NAME} | Build: #${env.BUILD_NUMBER} | Estado: ÉXITO"
        }
    }
}
