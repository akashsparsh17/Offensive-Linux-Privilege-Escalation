# Offensive Linux Privilege Escalation

Welcome to the **Offensive Linux Privilege Escalation** repository! This project is dedicated to documenting various techniques, methods, and exploits used to escalate privileges on Linux systems, primarily for ethical hacking, penetration testing, and cybersecurity research.

## 🔥 About the Repository

This repository provides a collection of privilege escalation techniques categorized by different attack vectors, including misconfigurations, software vulnerabilities, and system weaknesses. The content is intended for **educational and research purposes only** to help security professionals and ethical hackers understand and mitigate privilege escalation risks.

## 📂 Repository Structure

The repository consists of various Markdown (`.md`) files covering different privilege escalation techniques, such as:

- **[Access Control Lists (ACLs)](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Operating-System-and-Kernel-Details.md)** – Misconfigurations and bypass techniques.
- **[Check Permissions](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Check-Permission.md)** – Identifying weak file and directory permissions.
- **[Systemd Exploits](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Ecalate-via-systemd.md)** – Exploiting systemd misconfigurations for privilege escalation.
- **[Password Mining](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Enumeration-via-Password-Mining.md)** – Extracting and cracking credentials stored on the system.
- **[Crontab Escalation](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Escalate-via-Cron.md)** – Gaining root access through improperly configured cron jobs.
- **[Capabilities Abuse](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Escalate-via-capabilities.md)** – Exploiting Linux capabilities for privilege escalation.
- **[NFS Exploits](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Escalate-via-nfs.md)** – Abusing NFS shares for privilege escalation.
- **[Path Hijacking](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Escalate-via-path-hijacking.md)** – Manipulating `PATH` environment variables for privilege escalation.
- **[Sudo Exploits](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Escalate-via-sudo.md)** – Misconfigurations in sudo rules leading to privilege escalation.
- **[SQL on root (UDF)](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Escalate_via_SQL_on_root_(UDF).md)** – Using UDF (User Defined Functions) for privilege escalation.
- **[Shared Library Misconfigurations](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Exploiting-Shared-Library-Misconfigurations.md)** – Exploiting shared library loading mechanisms.
- **[Network Enumeration](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Network-Enumeration.md)** – Identifying network services and misconfigurations.
- **[Kernel & OS Enumeration](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/Operating-System-and-Kernel-Details.md)** – Gathering system and kernel details for exploitation.
- **[User Home Directory Enumeration](https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation/blob/main/User-Home-Directory-Enum.md)** – Enumerating user home directories for sensitive data and misconfigurations.


## 🚀 Getting Started

If you’re interested in privilege escalation techniques, follow these steps:

### Clone the repository

```bash
git clone https://github.com/InfoSecWarrior/Offensive-Linux-Privilege-Escalation.git
```
```bash
cd Offensive-Linux-Privilege-Escalation
```

### Browse the documentation

Each technique is explained in its respective `.md` file with detailed steps and examples.

### Test in a safe environment

⚠️ **WARNING**: Always test these techniques in a controlled environment, such as a virtual machine or a lab setup. Never attempt unauthorized exploitation.

## ⚠️ Disclaimer

This repository is for **educational and research purposes only**. Unauthorized use of these techniques in real-world systems without permission is illegal and unethical. The author is not responsible for any misuse of the information provided.

## 🛠 Contributing

Contributions are welcome! If you’d like to add new techniques or improve existing ones, feel free to:

- Open an **issue** for discussions.
- Submit a **pull request** with improvements.

## 📜 License

This project is licensed under the **MIT License** – see the [`LICENSE`](LICENSE) file for details.
