# Steps to install Azure CLI on Rocky Linux 10

# Import the Microsoft repository signing key.
'''
sudo rpm --import https://packages.microsoft.com/keys/microsoft-2025.asc
'''

# Add the Azure CLI software repository.
'''
sudo dnf install -y https://packages.microsoft.com/config/rhel/10/packages-microsoft-prod.rpm
'''

# Install the Azure CLI package.
'''
sudo dnf install azure-cli
'''

# Verify Installation
'''
az version
'''

# Sign In
'''
az login
'''
