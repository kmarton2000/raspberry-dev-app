# Raspberry Pi Zero W DEV Environment Pipeline Setup

Ez a dokumentáció bemutatja, hogyan építettünk ki egy Docker + Ansible alapú CI/CD deployment folyamatot Jenkinsből egy Raspberry Pi Zero W (ARMv6) edge eszközre.

---

## 1. Architektúra Összefoglaló

* **CI/CD Controller & Builders:** Jenkins MicroK8s Kubernetes clusterben.
* **Target Node:** Raspberry Pi Zero W (ARMv6, 512MB RAM, Docker engedélyezve).
* **Deploy Stratégia:** Remote Ansible push SSH-n keresztül.
* **Architektúra-specifikus megoldás:** ARMv6-kompatibilis Docker image build (`nginx:alpine` alapon).

---

## 2. Repó Struktúra

```text
raspberry-dev-app/
├── app/
│   ├── index.html
│   └── Dockerfile
├── ansible/
│   ├── inventory.ini
│   └── deploy.yml
├── Dockerfile.ansible
└── Jenkinsfile
```

## 3. Raspi user konfiguráció

```
# 1. 'deploy' user létrehozása és hozzáadása a docker csoporthoz
sudo useradd -m -s /bin/bash deploy
sudo usermod -aG docker deploy

# 2. Váltás a deploy userre
sudo su - deploy

# 3. SSH kulcspár generálása
ssh-keygen -t ed25519 -C "jenkins-deploy@raspberry" -N ""

# 4. Authorized keys beállítása
mkdir -p ~/.ssh && chmod 700 ~/.ssh
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys

# 5. Privát kulcs kiíratása (Jenkins Credentials-höz)
cat ~/.ssh/id_ed25519
```