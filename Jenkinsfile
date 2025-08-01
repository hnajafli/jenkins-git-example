pipeline {
    agent any // Pipeline-ın istənilən mövcud agentdə işləməsinə icazə verir

   // tools {
        // Bu, Jenkins agentində Python-un olması lazım olduğunu göstərir.
        // Əgər Jenkins agentinizdə Python yoxdursa, aşağıdakı addımı yerinə yetirməlisiniz:
        // Manage Jenkins -> Tools -> Python installations -> Add Python
        // Adı 'Python3' və ya istədiyiniz başqa bir şey ola bilər.
        // Daha sonra burada o adı qeyd etməlisiniz.
        // Əgər agentinizdə Python əvvəldən quraşdırılıbsa və PATH-dadırsa, bu hissəyə ehtiyac qalmır.
        // Bizim halımızda Ubuntu VM-də Python var, o yüzden bu sətirlərə ehtiyac qalmaya bilər.
        // python 'Python3' // Misal üçün
    // }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Kodu Git deposundan çəkir...'
                // Bu addım "Pipeline script from SCM" seçildiyi üçün avtomatik olaraq işləyir.
                // Lakin, adətən explicit olaraq scm checkout etmək daha yaxşıdır.
                // Aşağıdakı əmr, pipeline-ın quraşdırıldığı Git deposunu çəkməsini təmin edir.
                git branch: 'main', url: 'https://github.com/hnajafli/jenkins-git-example.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Pytest sistemdə quraşdırılıb...'
                // script {
                    // 1. Virtual mühiti yaradırıq (adətən 'venv' adlı bir qovluqda)
                    // sh 'python3 -m venv venv'
                    // 2. Virtual mühiti aktivləşdiririk (Python və pip əmrləri artıq bu mühitdə işləyəcək)
                    // Jenkinsfile-da `sh` istifadə edərkən aktivləşdirmə bir qədər fərqli olur.
                    // Biz birbaşa virtual mühitin içindəki python/pip-i çağıracağıq.
                    // sh 'venv/bin/python3 -m pip install --upgrade pip' // pip-i yeniləyirik
                    // sh 'venv/bin/python3 -m pip install pytest' // pytest-i quraşdırırıq
                // }
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Testləri işlədir...'
                script {
                    try {
                        // Testləri virtual mühitdəki python ilə işlədirik
                        sh 'python3 -m pytest test_main.py'
                        echo 'Bütün testlər uğurla keçdi!'
                    } catch (err) {
                        echo "Testlər uğursuz oldu: ${err}"
                        currentBuild.result = 'FAILURE'
                        throw err
                    }
                }
            }
        }

        stage('Report Status') {
            steps {
                echo "Testlərin statusu: ${currentBuild.result}"
                script {
                    if (currentBuild.result == 'SUCCESS') {
                        echo 'Testlər uğurlu oldu. Sonrakı mərhələyə keçirik.'
                    } else {
                        echo 'Testlər uğursuz oldu. Deploy edilməyəcək.'
                    }
                }
            }
        }

        stage('Deploy') {
            // Testlər uğurlu olduqda bu mərhələyə keçsin
            when {
                expression { currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Tətbiq uğurla deploy edilir (simulyasiya)...'
                // Burada real deploy əmrləri olacaqdı (Docker build, Kubernetes deploy, SFTP, və s.)
                sh 'echo "Deployment completed successfully!"'
            }
        }
    }

    post {
        always {
            echo 'Pipeline tamamlandı.'
        }
        success {
            echo 'Pipeline uğurla başa çatdı!'
            // Burada uğurlu build üçün bildirim göndərə bilərsiniz (email, Slack və s.)
        }
        failure {
            echo 'Pipeline uğursuz oldu!'
            // Burada uğursuz build üçün bildirim göndərə bilərsiniz (email, Slack və s.)
        }
    }
}
