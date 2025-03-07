pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Detectar archivos modificados') {
            steps {
                script {
                    echo "🔍 Detectando cambios en el repositorio..."

                    // Mostrar información de la rama en la que se ejecuta el pipeline
                    echo "🌿 Rama actual: ${env.BRANCH_NAME}"
                    echo "📌 Rama origen (PR): ${env.CHANGE_BRANCH}"
                    echo "🎯 Rama destino (PR): ${env.CHANGE_TARGET}"

                    // Obtener lista de archivos modificados en el último commit
                    def changedFiles = sh(
                        script: "git diff --name-status HEAD~1 HEAD",
                        returnStdout: true
                    ).trim()

                    if (changedFiles) {
                        echo "Archivos modificados en el último push:"
                        echo "${changedFiles}"
                        
                        // Guardar en un archivo
                        writeFile file: 'changed-files.txt', text: changedFiles
                        archiveArtifacts artifacts: 'changed-files.txt', fingerprint: true
                    } else {
                        echo "No se detectaron cambios en archivos."
                    }
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
