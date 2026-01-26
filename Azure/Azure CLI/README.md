# Steps to install Azure CLI on Rocky Linux 10

# Import the Microsoft repository signing key.
```cmd
sudo rpm --import https://packages.microsoft.com/keys/microsoft-2025.asc
```

# Add the Azure CLI software repository.
```cmd
sudo dnf install -y https://packages.microsoft.com/config/rhel/10/packages-microsoft-prod.rpm
```

# Install the Azure CLI package.
```cmd
sudo dnf install azure-cli
```

# Verify Installation
```cmd
az version
```

# Sign In
```cmd
az login
```
