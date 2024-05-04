# Using SSH on Linux

SSH (Secure Shell) is a network protocol that allows secure remote access to a server over an encrypted connection. It provides a secure way to access and manage remote systems.

## Installation

Most Linux distributions come with SSH installed by default. However, if it's not installed, you can install it using your package manager.

For example, on Debian/Ubuntu:

```bash
sudo apt update
sudo apt install openssh-server
```

On CentOS/RHEL:

```bash
sudo yum install openssh-server
```

## Connecting to a Remote Server

To connect to a remote server using SSH, use the `ssh` command followed by the username and hostname or IP address of the remote server.

```bash
ssh username@hostname_or_ip
```

For example:

```bash
ssh user@example.com
```

If you're using a different SSH port (other than the default port 22), you can specify it using the `-p` option:

```bash
ssh -p port_number username@hostname_or_ip
```

## Authentication

SSH supports various methods of authentication:

### Password Authentication

The most common method is password authentication, where you provide your username and password when prompted.

```bash
ssh username@hostname_or_ip
```

### Key-based Authentication

Key-based authentication is more secure than password authentication. It uses public-key cryptography to authenticate users.

1. Generate an SSH key pair on your local machine if you don't have one already:

```bash
ssh-keygen -t rsa -b 2048
```

2. Copy your public key to the remote server:

```bash
ssh-copy-id username@hostname_or_ip
```

3. You'll be prompted to enter your password. Once authenticated, your public key will be added to the `~/.ssh/authorized_keys` file on the remote server.

4. Now, you can SSH to the server without entering a password:

```bash
ssh username@hostname_or_ip
```

### Other Authentication Methods

SSH also supports other authentication methods like keyboard-interactive authentication and certificate-based authentication.

## File Transfer with SCP and SFTP

SSH also provides utilities for secure file transfer:

### SCP (Secure Copy)

SCP allows you to securely copy files between hosts.

```bash
scp /path/to/local/file username@hostname_or_ip:/path/to/remote/location
```

### SFTP (SSH File Transfer Protocol)

SFTP provides a secure way to transfer files between hosts, similar to FTP.

```bash
sftp username@hostname_or_ip
```
