node('custom-buildx-agent') {
    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
        
        stage('1. Checkout Code') {
            checkout scm
        }

        stage('2. Build & Push Native ARMv6 Image') {
            container('buildx') {
                sh 'git config --global --add safe.directory "*"'

                def dateStr = sh(script: "date +%Y%m%d%H%M", returnStdout: true).trim()
                def gitCommit = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                env.IMAGE_TAG = "v${dateStr}-${gitCommit}"
                
                def randomHue = new Random().nextInt(360)
                env.BG_COLOR = "hsl(${randomHue}, 70%, 85%)"

                echo "===> Generált Verzió: ${env.IMAGE_TAG}, Szín: ${env.BG_COLOR}"

                sh '''
                    # Kernel binfmt QEMU regisztráció cross-platform buildhez
                    docker run --privileged --rm tonistiigi/binfmt --install all

                    # Docker Hub autentikáció
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    # Buildx builder példány létrehozása és aktiválása
                    docker buildx create --name armv6builder --driver docker-container --use || true
                    docker buildx inspect --bootstrap

                    # Tényleges ARMv6 build és push
                    cd app
                    docker buildx build \
                      --platform linux/arm/v6 \
                      --build-arg IMAGE_TAG=${IMAGE_TAG} \
                      --build-arg BACKGROUND_COLOR="${BG_COLOR}" \
                      -t $DOCKER_USER/raspberry-dev-app:${IMAGE_TAG} \
                      --push .
                '''
            }
        }
    }
}

node('custom-ansible-agent') {
    withCredentials([
        usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS'),
        sshUserPrivateKey(credentialsId: 'raspberry-ssh-key', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')
    ]) {
        stage('3. Deploy to Raspberry Pi via Ansible') {
            container('ansible') {
                checkout scm

                sh '''
                    mkdir -p ~/.ssh
                    echo "Host *" > ~/.ssh/config
                    echo "  StrictHostKeyChecking no" >> ~/.ssh/config
                    chmod 600 ${SSH_KEY}
                    
                    ansible-playbook -i ansible/inventory.ini ansible/deploy.yml \
                      --private-key=${SSH_KEY} \
                      --extra-vars "ansible_user=${SSH_USER}"
                '''
            }
        }
    }
}