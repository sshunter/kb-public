# WSL2 GIT CREDENTIALS

This script is a what gemini spit out when I asked it to docment the process it
walked me thru. I have never run it.

```bash
#!/bin/bash
# ==============================================================================
# WSL2 GIT CREDENTIAL MANAGER + GPG + PASS SETUP SCRIPT
# ==============================================================================
# INSTRUCTIONS: Edit the two variables below, copy this entire block, paste
# it into your WSL2 terminal, and run it.
# ==============================================================================

FORGEJO_EMAIL="your-email@example.com"
FORGEJO_DOMAIN="forgejo.example.com"

echo "=== 1. Installing System Packages ==="
sudo apt update && sudo apt install -y gnupg pass pinentry-tty wget

echo "=== 2. Configuring GPG Directories & Permissions ==="
mkdir -p ~/.gnupg
chmod 700 ~/.gnupg

echo "=== 3. Configuring TTY-only Pinentry & Agent Cache ==="
cat << 'EOF' > ~/.gnupg/gpg-agent.conf
pinentry-program /usr/bin/pinentry-tty
default-cache-ttl 604800
max-cache-ttl 2419200
EOF
chmod 600 ~/.gnupg/*

sudo update-alternatives --set pinentry /usr/bin/pinentry-tty

if ! grep -q "GPG_TTY" ~/.bashrc; then
    echo 'export GPG_TTY=$(tty)' >> ~/.bashrc
fi
export GPG_TTY=$(tty)
gpg-connect-agent reloadagent /bye

echo "=== 4. Downloading and Installing Git Credential Manager ==="
wget https://github.com/git-ecosystem/git-credential-manager/releases/download/v2.8.0/gcm-linux-x64-2.8.0.deb
sudo apt install -y ./gcm-linux-x64-2.8.0.deb
rm ./gcm-linux-x64-2.8.0.deb
git-credential-manager configure

echo "=== 5. Binding GCM to GPG and Scoping Domain ==="
git config --global credential.credentialStore gpg
git config --global credential.https://${FORGEJO_DOMAIN}.helper manager

echo "=========================================================="
echo " INTERACTIVE STEP REQUIRED                                "
echo "=========================================================="
echo "The script will now generate your GPG key."
echo "CRITICAL: Use the exact email: $FORGEJO_EMAIL"
echo "=========================================================="
read -p "Press [ENTER] to start GPG key generation..."

gpg --generate-key

echo "=== 6. Initializing Password Store ==="
pass init "$FORGEJO_EMAIL"

echo "=========================================================="
echo " SETUP COMPLETE                                           "
echo "=========================================================="
echo "Run 'source ~/.bashrc' to update your current shell."
echo "Your next git push/pull to $FORGEJO_DOMAIN will prompt for credentials."
```
