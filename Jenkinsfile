def produccion = [:]
            produccion.name = 'practicajenkins'
            produccion.host = '172.17.0.2'   // modificar con IP de contenedor docker con Tomcat
            produccion.user = 'root'
            produccion.password = 'root'
            produccion.allowAnyHosts = true
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                
                //Creo el directorio del código. Si no existe se crea
                
                dir('codigo') {
                    // Get some code from a GitHub repository
                    git 'https://github.com/jleetutorial/maven-project.git'

                    // Run Maven on a Unix agent.
                    sh "mvn clean package -DskipTests"

                    // To run Maven on a Windows agent, use
                    // bat "mvn -Dmaven.test.failure.ignore=true clean package"    
                }
                
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    archiveArtifacts 'codigo/webapp/target/*.war'
                }
            }
        }
        stage('Test') {
            steps {
                sh "echo 'Realización de algunos Test'"    
            }
            
        }
        stage('Container') {
            steps {
                sh "echo 'Creo el contenedor'"
                dir('contenedor') {
                    withCredentials([usernamePassword(credentialsId: 'MI_ID_EN_GITHUB', passwordVariable: 'password', usernameVariable: 'usuario')]) {
                        git 'URL_DE_MI_REPOSITORIO_JENKINS.git'
                        sh('''
                            cp ../codigo/webapp/target/webapp.war .
                            git add .
                            git config --global user.email 'MI_DIRECCION_DE@CORREO.ES'
                            git config --global user.name  'MI_NOMBRE_DE_USUARIO'
                            git commit -m 'Desde Jenkinsfile'
                            git config --local credential.helper "!f() { echo username=\\$usuario; echo password=\\$password; }; f"
                            git push origin master
                        ''')
                    }
                }
                
            }
        }
        stage('Deploy') {
            steps {
                sh "echo 'Desplegando'"    
                sshPut remote: produccion, from: 'codigo/webapp/target/webapp.war' , into: '/usr/local/tomcat/webapps/'    
            }
        }
    }
}