pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Show Changed Files') {
            steps {
                script {
                    // Si es un PR
                    if (env.CHANGE_ID) {
                        echo "Pull Request #${env.CHANGE_ID} detectado"
                        
                        // Obtener la lista de archivos modificados
                        def changedFiles = sh(
                            script: "git diff --name-status origin/${env.CHANGE_TARGET} HEAD",
                            returnStdout: true
                        ).trim()
                        
                        echo "Archivos modificados en este PR:"
                        echo "${changedFiles}"
                        
                        // Opcionalmente, guardar la lista de archivos para su uso posterior
                        writeFile file: 'changed-files.txt', text: changedFiles
                        archiveArtifacts artifacts: 'changed-files.txt', fingerprint: true
                    } else {
                        echo "No es un PR, es un build regular de rama"
                        
                        // Para builds regulares, muestra los últimos cambios
                        def recentChanges = sh(
                            script: "git diff --name-status HEAD~1 HEAD",
                            returnStdout: true
                        ).trim()
                        
                        echo "Cambios recientes en esta rama:"
                        echo "${recentChanges}"
                    }
                }
            }
        }
        
        // Aquí puedes añadir más etapas para tu pipeline
    }
    
    post {
        always {
            // Limpieza y notificaciones
            cleanWs()
        }
    }
}