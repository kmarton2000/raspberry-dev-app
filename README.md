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