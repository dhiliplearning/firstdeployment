pipeline {
  agent any
  stages {
    stage('Build & Test') {
      steps {
        echo 'Run your build/tests here'
        // e.g., npm ci && npm test  OR  mvn -B -ntp verify  OR  pytest -q
      }
    }

    // Skip deploy on PRs; only deploy on normal branch builds (e.g., main)
    stage('Deploy to EC2') {
      when { not { changeRequest() } }     // remove this line if you want PRs to deploy too
      steps {
        sshagent(credentials: ['dd0c93f1-a7a9-4788-8390-bdc8a3699679']) {
          sh '''
            set -e
            REMOTE_USER="ec2-user"              # use "ubuntu" for Ubuntu AMIs
            REMOTE_HOST="<EC2_IP>"
            DEPLOY_PATH="<DEPLOY_PATH>"         # e.g. /var/www/html or /opt/app

            # Ensure target directory exists
            ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST "sudo mkdir -p $DEPLOY_PATH && sudo chown -R $REMOTE_USER:$REMOTE_USER $DEPLOY_PATH"

            # Copy files from current workspace to EC2
            # (exclude .git and Jenkins workspace junk)
            rsync -az --delete \
              --exclude ".git" \
              --exclude ".gitignore" \
              --exclude "node_modules" \
              ./ $REMOTE_USER@$REMOTE_HOST:$DEPLOY_PATH/

            # Optional: restart a service (uncomment if needed)
            # ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST "sudo systemctl restart nginx || true"
          '''
        }
      }
    }
  }
}
