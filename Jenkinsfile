pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Check Secrets') {
            steps {
                script {
                    try {
                        sh 'git-secrets --scan'
                    } catch (Exception e) {
                        echo "Se encontraron secretos en el código. Revisa antes de continuar."
                        error("Pipeline detenido por exposición de credenciales.")
                    }
                }
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
            echo " Despliegue completado exitosamente"
            githubNotify context: 'Jenkins CI', status: 'SUCCESS', description: 'Build exitoso'*/
            
            //echo " El despliegue falló"
            //githubNotify context: 'Jenkins CI', status: 'FAILURE', description: 'El build falló'   
        }
    }

}
