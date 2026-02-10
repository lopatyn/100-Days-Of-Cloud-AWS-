# Day 1: Create Key Pair 🔑

> **Social Summary (Ukrainian):**
> **Problem:** Як забезпечити безпечний та контрольований доступ до хмарної інфраструктури з першого дня?
> **Solution:** Впровадження RSA Key Pairs з дотриманням принципів Hardening та секретного зберігання.
> **Value:** Nautilus DevOps отримує фундамент для безпечної міграції, де кожен ключ захищений, а репозиторій залишається чистим від секретів.

## Overview
Key Pairs are the fundamental mechanism for establishing secure SSH connections to EC2 instances in AWS. In this initial stage of the Nautilus DevOps cloud migration, we focus on generating and properly managing these cryptographic keys. Beyond just "creating a file," we implement **SecOps best practices** by ensuring private keys are stored outside the version control system and handled with restricted filesystem permissions.

## Practical Tasks
- [x] Create an RSA Key Pair named `xfusion-kp`.
- [x] Implement a `.gitignore` policy to prevent accidental secret leakage.
- [x] Refactor local storage to move keys to a secure system directory (`~/.ssh/`).
- [x] Verify file permissions to satisfy SSH client security requirements.

## Architecture & Implementation
We use the AWS Management Console to generate the key pair. AWS retains the **Public Key**, while the **Private Key** is downloaded to the local administrative workstation.

```mermaid
graph TD
    subgraph AWS_Cloud ["AWS Global Infrastructure"]
        EC2_Service["EC2 Key Pairs Service"]
        Public_Key_Store[("AWS Key Store (Public Key)")]
    end

    subgraph Local_Workstation ["Engineer's Local Machine"]
        Admin_User["Nautilus DevOps Engineer"]
        PEM_File["xfusion-kp.pem (Private Key)"]
        SSH_Config["~/.ssh/ directory (Secure)"]
        Git_Repo["Project Repository (Public/Private)"]
    end

    Admin_User -- "1. Request Creation (RSA)" --> EC2_Service
    EC2_Service -- "2. Store Public Part" --> Public_Key_Store
    EC2_Service -- "3. Generate & Download .pem" --> PEM_File
    PEM_File -- "4. Move for Security" --> SSH_Config
    Git_Repo -- "5. Hardened via .gitignore" -.-> PEM_File
```

## Key Commands
```powershell
# Create standard secure directory if it doesn't exist (Windows/PowerShell)
mkdir ~\.ssh -Force

# Move the downloaded key to the secure location
# This keeps the project repository "Stateless" and secure
move-item .\xfusion-kp.pem ~\.ssh\xfusion-kp.pem

# Secure the repository from accidental commits
echo "xfusion-kp.pem" >> .gitignore
```

## Security Insights 🛡️
As Senior DevOps Engineers, we analyze this task through the **AWS Well-Architected Framework**:
- **Security Pillar (Least Privilege):** Modern SSH clients (OpenSSH) will reject a private key if it has broad permissions (e.g., `644`). The key must be "read-only" for the owner (`400`).
- **Secret Management:** Storing secrets in Git is a critical vulnerability. Even if the repo is private, it increases the blast radius of a credential leak.
- **Persistence:** Since AWS does not store the private key, losing the `.pem` file means losing access to instances (unless alternative access like **SSM Session Manager** is configured).

---
**Next Steps:** Prepare for Day 2: Security Groups! 🚀
