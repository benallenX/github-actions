# SSH Keys for GitHub Actions Project

## Overview
This project includes SSH keys for secure authentication and deployment purposes.

## Files
- `.ssh/id_ed25519.pub` - Public SSH key (safe to share)
- `.ssh/id_ed25519` - Private SSH key (NEVER share or commit)

## Key Details
- **Algorithm**: ED25519 (modern, secure, and fast)
- **Comment**: github-actions-project
- **Public Key**: Available in `.ssh/id_ed25519.pub`

## Usage

### Adding the Public Key to a Server
To allow this project to authenticate with a remote server:

1. **Recommended method using ssh-copy-id:**
   ```bash
   ssh-copy-id -i .ssh/id_ed25519.pub user@remote-server
   ```

2. **Manual method:**
   ```bash
   cat .ssh/id_ed25519.pub | ssh user@remote-server "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
   ```

### Adding the Public Key to GitHub
To use this key for GitHub operations:

1. Copy the public key content from `.ssh/id_ed25519.pub`
2. Go to GitHub Settings → SSH and GPG keys
3. Click "New SSH key"
4. Paste the public key and save

### Using in GitHub Actions
To use the SSH key in GitHub Actions workflows:

1. Add the private key as a GitHub Secret:
   - Go to repository Settings → Secrets and variables → Actions
   - Create a new secret (e.g., `SSH_PRIVATE_KEY`)
   - Paste the content of `.ssh/id_ed25519`

2. **Recommended: Use a secure SSH action:**
   ```yaml
   - name: Setup SSH
     uses: webfactory/ssh-agent@v0.9.0
     with:
       ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}
   ```

3. **Alternative manual setup (less secure):**
   ```yaml
   - name: Setup SSH
     env:
       SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
     run: |
       mkdir -p ~/.ssh
       chmod 700 ~/.ssh
       echo "$SSH_PRIVATE_KEY" > ~/.ssh/id_ed25519
       chmod 600 ~/.ssh/id_ed25519
       ssh-keyscan github.com >> ~/.ssh/known_hosts
   ```

### Testing the Connection
Test SSH connection to GitHub:
```bash
ssh -T git@github.com -i .ssh/id_ed25519
```

Test SSH connection to a server:
```bash
ssh -i .ssh/id_ed25519 user@remote-server
```

## Security Notes
⚠️ **IMPORTANT**:
- The private key (`.ssh/id_ed25519`) is protected by `.gitignore` and should NEVER be committed
- Only share the public key (`.ssh/id_ed25519.pub`)
- Store the private key securely (e.g., in GitHub Secrets for CI/CD)
- If the private key is compromised, regenerate a new key pair immediately

## Regenerating Keys
If you need to generate new keys:

```bash
# Remove old keys
rm -f .ssh/id_ed25519 .ssh/id_ed25519.pub

# Generate new key pair
ssh-keygen -t ed25519 -C "github-actions-project" -f .ssh/id_ed25519 -N ""
```

## Key Fingerprint
You can verify the key fingerprint with:
```bash
ssh-keygen -lf .ssh/id_ed25519.pub
```
