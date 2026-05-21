# Installation-de-Qemu-et-Hexagon_DSP# 🖥️ QEMU — Guide d'installation complet

<div align="center">

![QEMU Logo](https://img.shields.io/badge/QEMU-FF6600?style=for-the-badge&logo=qemu&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![macOS](https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white)

**Guide d'installation et de configuration de QEMU sur Linux, Windows et macOS**

</div>

---

## 📋 Table des matières

- [À propos de QEMU](#-à-propos-de-qemu)
- [Prérequis](#-prérequis)
- [Installation sur Linux](#-installation-sur-linux)
  - [Debian / Ubuntu](#debian--ubuntu)
  - [Fedora / RHEL / CentOS](#fedora--rhel--centos)
  - [Arch Linux / Manjaro](#arch-linux--manjaro)
  - [openSUSE](#opensuse)
  - [Compilation depuis les sources](#compilation-depuis-les-sources)
- [Installation sur Windows](#-installation-sur-windows)
- [Installation sur macOS](#-installation-sur-macos)
- [Vérification de l'installation](#-vérification-de-linstallation)
- [Activation de KVM (Linux)](#-activation-de-kvm-linux)
- [Utilisation de base](#-utilisation-de-base)
  - [Créer une image disque](#créer-une-image-disque)
  - [Lancer une VM](#lancer-une-vm)
  - [Exemples courants](#exemples-courants)
- [Gestion avec libvirt / virt-manager](#-gestion-avec-libvirt--virt-manager)
- [Configuration réseau](#-configuration-réseau)
- [Dépannage](#-dépannage)
- [Ressources utiles](#-ressources-utiles)
- [Licence](#-licence)

---

## 📖 À propos de QEMU

**QEMU** (Quick EMUlator) est un émulateur de machines et un hyperviseur open-source capable de :

- **Émuler** différentes architectures matérielles (x86, ARM, MIPS, RISC-V, PowerPC, etc.)
- **Virtualiser** des systèmes d'exploitation à des performances quasi-natives grâce à **KVM** (Kernel-based Virtual Machine) sur Linux
- Fonctionner en mode **utilisateur** (émulation d'un seul processus) ou en mode **système** (émulation d'une machine complète)

| Fonctionnalité | Description |
|---|---|
| 🏎️ Performances | Accélération matérielle avec KVM/HVF/WHPX |
| 🔄 Multi-architecture | x86, x86_64, ARM, ARM64, RISC-V, MIPS… |
| 🌐 Réseau | NAT, bridge, user-mode networking |
| 💾 Formats disque | qcow2, raw, vmdk, vhd, vdi… |
| 📸 Snapshots | Sauvegarde et restauration d'états |
| 🔌 USB/PCI | Passthrough matériel |

---

## ⚙️ Prérequis

### Matériel recommandé

| Composant | Minimum | Recommandé |
|---|---|---|
| CPU | 64 bits avec virtualisation | Intel VT-x / AMD-V activé |
| RAM | 4 Go | 8 Go ou plus |
| Stockage | 10 Go libres | SSD recommandé |

### Vérifier la prise en charge de la virtualisation

```bash
# Linux — vérifie si le CPU supporte KVM
egrep -c '(vmx|svm)' /proc/cpuinfo
# Résultat > 0 = virtualisation supportée

# Ou avec la commande :
lscpu | grep Virtualization
```

> **Note :** Si la virtualisation n'apparaît pas, vérifiez que VT-x/AMD-V est activé dans le BIOS/UEFI.

---

## 🐧 Installation sur Linux

### Debian / Ubuntu

```bash
# Mettre à jour les paquets
sudo apt update && sudo apt upgrade -y

# Installer QEMU et les outils associés
sudo apt install -y \
  qemu-system \
  qemu-utils \
  qemu-kvm \
  libvirt-daemon-system \
  libvirt-clients \
  bridge-utils \
  virtinst \
  virt-manager

# Ajouter votre utilisateur aux groupes requis
sudo usermod -aG kvm,libvirt $(whoami)

# Activer et démarrer libvirtd
sudo systemctl enable --now libvirtd
```

> ⚠️ **Reconnectez-vous** à votre session après avoir ajouté votre utilisateur aux groupes.

---

### Fedora / RHEL / CentOS

```bash
# Fedora
sudo dnf install -y \
  @virtualization \
  qemu-kvm \
  libvirt \
  libvirt-client \
  virt-manager \
  virt-install

# RHEL 8 / CentOS Stream 8
sudo dnf module install virt -y
sudo dnf install -y \
  qemu-kvm \
  libvirt \
  virt-manager \
  virt-install

# Ajouter l'utilisateur aux groupes
sudo usermod -aG kvm,libvirt $(whoami)

# Activer le service
sudo systemctl enable --now libvirtd
```

---

### Arch Linux / Manjaro

```bash
# Installer QEMU et les dépendances
sudo pacman -S --needed \
  qemu-full \
  libvirt \
  virt-manager \
  virt-viewer \
  dnsmasq \
  bridge-utils \
  openbsd-netcat \
  ebtables \
  iptables

# Ajouter l'utilisateur aux groupes
sudo usermod -aG kvm,libvirt $(whoami)

# Activer le service
sudo systemctl enable --now libvirtd
```

---

### openSUSE

```bash
# openSUSE Leap / Tumbleweed
sudo zypper install -y \
  qemu \
  qemu-kvm \
  libvirt \
  virt-manager \
  virt-install \
  bridge-utils

# Ajouter l'utilisateur aux groupes
sudo usermod -aG kvm,libvirt $(whoami)

# Activer le service
sudo systemctl enable --now libvirtd
```

---

### Compilation depuis les sources

Pour obtenir la dernière version ou une version personnalisée :

```bash
# Installer les dépendances de compilation (Debian/Ubuntu)
sudo apt install -y \
  git \
  build-essential \
  ninja-build \
  pkg-config \
  python3-pip \
  libglib2.0-dev \
  libpixman-1-dev \
  libsdl2-dev \
  libgtk-3-dev \
  libvte-2.91-dev \
  libspice-server-dev \
  libusb-1.0-0-dev \
  zlib1g-dev \
  libcap-ng-dev

# Cloner le dépôt officiel
git clone https://gitlab.com/qemu-project/qemu.git
cd qemu
git checkout v9.2.0  # remplacez par la version souhaitée

# Configurer la compilation
./configure \
  --target-list=x86_64-softmmu,aarch64-softmmu \
  --enable-kvm \
  --enable-sdl \
  --enable-gtk \
  --enable-spice \
  --enable-usb-redir \
  --prefix=/usr/local

# Compiler et installer
make -j$(nproc)
sudo make install
```

---

## 🪟 Installation sur Windows

### Via l'installateur officiel

1. Rendez-vous sur le site officiel de QEMU pour Windows : [https://www.qemu.org/download/#windows](https://www.qemu.org/download/#windows)
2. Téléchargez le fichier `.exe` correspondant à votre architecture (64 bits recommandé)
3. Lancez l'installateur et suivez les instructions

### Ajouter QEMU au PATH

```powershell
# PowerShell — ajouter QEMU au PATH système
$env:Path += ";C:\Program Files\qemu"
[System.Environment]::SetEnvironmentVariable("Path", $env:Path, [System.EnvironmentVariableTarget]::Machine)
```

### Activer Hyper-V / WHPX (accélération matérielle)

```powershell
# Activer Hyper-V (nécessite un redémarrage)
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All

# Vérifier que WHPX est disponible
qemu-system-x86_64 -accel whpx
```

> **Note :** Sur Windows, l'accélération matérielle utilise **WHPX** (Windows Hypervisor Platform) au lieu de KVM.

---

## 🍎 Installation sur macOS

### Via Homebrew (recommandé)

```bash
# Installer Homebrew si ce n'est pas déjà fait
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Installer QEMU
brew install qemu

# Vérifier l'installation
qemu-system-x86_64 --version
```

### Via MacPorts

```bash
sudo port install qemu
```

> **Note :** Sur les Mac avec puce **Apple Silicon (M1/M2/M3)**, QEMU utilise l'accélération **HVF** (Hypervisor Framework). Pour émuler une architecture différente (ex. x86_64), des performances réduites sont à prévoir.

---

## ✅ Vérification de l'installation

```bash
# Vérifier la version installée
qemu-system-x86_64 --version

# Lister les accélérateurs disponibles
qemu-system-x86_64 -accel help

# Vérifier que KVM est opérationnel (Linux)
kvm-ok
# ou
ls -la /dev/kvm
```

Résultat attendu :

```
QEMU emulator version 9.x.x
Copyright (c) 2003-2024 Fabrice Bellard and the QEMU Project developers
```

---

## ⚡ Activation de KVM (Linux)

### Vérifier et charger les modules KVM

```bash
# Pour les processeurs Intel
sudo modprobe kvm_intel

# Pour les processeurs AMD
sudo modprobe kvm_amd

# Vérifier que les modules sont bien chargés
lsmod | grep kvm
```

### Charger KVM au démarrage

```bash
# Intel
echo "kvm_intel" | sudo tee /etc/modules-load.d/kvm.conf

# AMD
echo "kvm_amd" | sudo tee /etc/modules-load.d/kvm.conf
```

### Vérifier les permissions sur /dev/kvm

```bash
ls -la /dev/kvm
# Devrait afficher : crw-rw---- 1 root kvm ...

# Si nécessaire, corriger les permissions
sudo chmod 660 /dev/kvm
sudo chown root:kvm /dev/kvm
```

---

## 🚀 Utilisation de base

### Créer une image disque

```bash
# Format qcow2 (recommandé — dynamique, snapshots, compression)
qemu-img create -f qcow2 disque.qcow2 20G

# Format raw (performances maximales)
qemu-img create -f raw disque.raw 20G

# Convertir une image d'un format à un autre
qemu-img convert -f raw -O qcow2 disque.raw disque.qcow2

# Afficher les informations d'une image
qemu-img info disque.qcow2
```

---

### Lancer une VM

#### Structure générale d'une commande QEMU

```bash
qemu-system-x86_64 \
  -enable-kvm \              # Activer l'accélération KVM
  -cpu host \                # Utiliser les capacités du CPU hôte
  -m 2G \                    # Mémoire RAM allouée
  -smp 2 \                   # Nombre de vCPUs
  -hda disque.qcow2 \        # Disque dur principal
  -cdrom image.iso \         # CD-ROM / ISO de démarrage
  -boot d \                  # Démarrer depuis le CD
  -vga virtio \              # Carte graphique virtuelle
  -net nic -net user \       # Réseau (mode NAT)
  -name "Ma VM"              # Nom de la VM
```

---

### Exemples courants

#### Installer une distribution Linux depuis un ISO

```bash
qemu-system-x86_64 \
  -enable-kvm \
  -cpu host \
  -m 4G \
  -smp 4 \
  -hda ubuntu.qcow2 \
  -cdrom ubuntu-24.04-desktop-amd64.iso \
  -boot d \
  -vga virtio \
  -display sdl \
  -net nic,model=virtio -net user \
  -name "Ubuntu 24.04"
```

#### Démarrer une VM existante (sans ISO)

```bash
qemu-system-x86_64 \
  -enable-kvm \
  -cpu host \
  -m 4G \
  -smp 4 \
  -hda ubuntu.qcow2 \
  -vga virtio \
  -net nic,model=virtio -net user,hostfwd=tcp::2222-:22 \
  -name "Ubuntu 24.04"
```

#### VM ARM64 (ex : Raspberry Pi / serveurs ARM)

```bash
qemu-system-aarch64 \
  -machine virt \
  -cpu cortex-a72 \
  -m 2G \
  -bios /usr/share/qemu-efi-aarch64/QEMU_EFI.fd \
  -hda arm64-disk.qcow2 \
  -cdrom debian-arm64.iso \
  -boot d \
  -nographic
```

#### Créer un snapshot et le restaurer

```bash
# Créer un snapshot
qemu-img snapshot -c "avant-mise-a-jour" disque.qcow2

# Lister les snapshots
qemu-img snapshot -l disque.qcow2

# Restaurer un snapshot
qemu-img snapshot -a "avant-mise-a-jour" disque.qcow2

# Supprimer un snapshot
qemu-img snapshot -d "avant-mise-a-jour" disque.qcow2
```

---

## 🖱️ Gestion avec libvirt / virt-manager

### virt-manager (interface graphique)

```bash
# Lancer virt-manager
virt-manager
```

virt-manager offre une interface graphique complète pour créer, gérer et surveiller les machines virtuelles via libvirt.

### virsh (interface en ligne de commande)

```bash
# Lister toutes les VMs
virsh list --all

# Démarrer une VM
virsh start nom-de-la-vm

# Arrêter une VM proprement
virsh shutdown nom-de-la-vm

# Forcer l'arrêt
virsh destroy nom-de-la-vm

# Suspendre / Reprendre
virsh suspend nom-de-la-vm
virsh resume nom-de-la-vm

# Supprimer une VM (sans supprimer les disques)
virsh undefine nom-de-la-vm

# Créer une VM via virt-install
virt-install \
  --name ubuntu-24 \
  --ram 4096 \
  --vcpus 4 \
  --disk path=ubuntu.qcow2,size=20 \
  --cdrom ubuntu-24.04-desktop-amd64.iso \
  --os-variant ubuntu24.04 \
  --graphics spice \
  --network network=default
```

---

## 🌐 Configuration réseau

### Mode NAT (par défaut — le plus simple)

```bash
-net nic,model=virtio -net user
```

### Redirection de ports (SSH, HTTP, etc.)

```bash
# Rediriger le port 2222 de l'hôte vers le port 22 de la VM (SSH)
-net nic,model=virtio -net user,hostfwd=tcp::2222-:22

# Rediriger le port 8080 vers le port 80 de la VM (HTTP)
-net nic,model=virtio -net user,hostfwd=tcp::8080-:80
```

Se connecter en SSH depuis l'hôte :

```bash
ssh -p 2222 utilisateur@127.0.0.1
```

### Mode Bridge (accès réseau complet)

```bash
# Créer un bridge br0 (Debian/Ubuntu)
sudo ip link add name br0 type bridge
sudo ip link set br0 up
sudo ip link set eth0 master br0
sudo dhclient br0

# Utiliser le bridge avec QEMU
-net nic,model=virtio -net bridge,br=br0
```

### Réseau avec libvirt (recommandé)

```bash
# Activer le réseau virtuel par défaut
virsh net-start default
virsh net-autostart default

# Lister les réseaux disponibles
virsh net-list --all
```

---

## 🛠️ Dépannage

### QEMU ne démarre pas avec KVM

```bash
# Vérifier si KVM est disponible
ls /dev/kvm

# Vérifier les permissions
groups $(whoami) | grep kvm

# Si le module n'est pas chargé
sudo modprobe kvm_intel   # ou kvm_amd
```

### Erreur : "Could not access KVM kernel module: Permission denied"

```bash
# Ajouter l'utilisateur au groupe kvm
sudo usermod -aG kvm $(whoami)

# Puis se déconnecter / reconnecter, ou forcer :
newgrp kvm
```

### Performances lentes

- Utiliser `-cpu host` pour passer les capacités réelles du CPU
- Utiliser `-enable-kvm` (Linux) ou `-accel hvf` (macOS)
- Utiliser des pilotes **VirtIO** pour le disque et le réseau (`-vga virtio`, `-net nic,model=virtio`)
- Utiliser le format **qcow2** pour les disques
- Allouer suffisamment de RAM et de vCPUs

### Problème d'affichage / pas de fenêtre

```bash
# Utiliser SDL
-display sdl

# Ou GTK
-display gtk

# Mode sans interface (serveur headless)
-nographic

# Utiliser SPICE (meilleures performances graphiques)
-vga qxl -spice port=5900,disable-ticketing=on
```

### L'image ISO ne démarre pas

```bash
# Vérifier l'ordre de démarrage
-boot order=dc   # d = CD-ROM, c = disque dur

# ou forcer le démarrage depuis le CD
-boot d
```

---

## 📚 Ressources utiles

| Ressource | Lien |
|---|---|
| 🌐 Site officiel QEMU | [https://www.qemu.org](https://www.qemu.org) |
| 📖 Documentation officielle | [https://www.qemu.org/docs/master/](https://www.qemu.org/docs/master/) |
| 💻 Code source (GitLab) | [https://gitlab.com/qemu-project/qemu](https://gitlab.com/qemu-project/qemu) |
| 🐛 Rapporter un bug | [https://gitlab.com/qemu-project/qemu/-/issues](https://gitlab.com/qemu-project/qemu/-/issues) |
| 💬 Mailing list | [https://lists.nongnu.org/mailman/listinfo/qemu-devel](https://lists.nongnu.org/mailman/listinfo/qemu-devel) |
| 📦 Wiki libvirt | [https://wiki.libvirt.org](https://wiki.libvirt.org) |
| 🖥️ virt-manager | [https://virt-manager.org](https://virt-manager.org) |
| 📺 Arch Wiki QEMU | [https://wiki.archlinux.org/title/QEMU](https://wiki.archlinux.org/title/QEMU) |

---

## 📝 Licence

Ce guide est distribué sous licence **MIT**. Vous êtes libre de le copier, modifier et redistribuer.

---

<div align="center">

Fait avec ❤️ — N'hésitez pas à ouvrir une **issue** ou une **pull request** pour améliorer ce guide !

⭐ Si ce guide vous a été utile, pensez à laisser une étoile !

</div>
