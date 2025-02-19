# TP-LVM-1

[![Visit Repository](https://img.shields.io/badge/Visit-Tekaya1%2FLab--LVM--1-blue?style=for-the-badge&logo=github)](https://github.com/Tekaya1/Lab-LVM-1)
# Gestion des Périphériques de Stockage sous Linux
## Afficher les Périphériques Blocs
```bash
lsblk
```
Affiche des informations sur les disques et partitions.

---

## Création de Partition
### 1️⃣ Disque MBR :
```bash
fdisk /dev/sdb
```
### 2️⃣ Disque GPT :
```bash
gdisk /dev/sdb
```
**Ou avec `parted` (compatible MBR et GPT) :**
```bash
parted /dev/sdb
```

---

## Configuration d'un Système de Fichiers
### 🖥 Windows : 
- **NTFS**
- **FAT32**

### 🐧 Linux : 
- **ext2**
- **ext3**
- **ext4**

#### Formatage en ext4
```bash
mkfs.ext4 /dev/sdb1
```
**Ou :**
```bash
mkfs -t ext4 /dev/sdb1
```

---

## Montage d'une Partition
### Montage Temporaire
```bash
mount /dev/sdb1 /mnt
```
**Ou :**
```bash
mkdir /rep
mount /dev/sdb1 /rep
```

### Montage Permanent
Modifier le fichier `/etc/fstab` :
```bash
vim /etc/fstab
```
Ajouter :
```
/dev/sdb1 /rep ext4 defaults 0 0
```
**Ou avec UUID** :
```bash
blkid /dev/sdb1
```
Puis ajouter dans `/etc/fstab` :
```
UUID=xxxxx-xxxxx /rep ext4 defaults 0 0
```

---

## Suppression d'une Partition
### 1️⃣ Démontage
```bash
umount /dev/sdb1
```
**Ou :**
```bash
umount /rep
```

### 2️⃣ Suppression avec `fdisk`
```bash
fdisk /dev/sdb
```
- **Appuyer sur `d`** pour supprimer une partition.
- **Sélectionner la partition**.
- **Appuyer sur `w`** pour enregistrer.

---

## Création et Configuration d’une Partition SWAP
### 1️⃣ Création de la partition swap
```bash
fdisk /dev/sdb
```
- **Créer une nouvelle partition (`n`)**.
- **Sélectionner le type `82` (Linux swap)**.
- **Enregistrer (`w`)**.

### 2️⃣ Formater en swap
```bash
mkswap /dev/sdb3
```

### 3️⃣ Activer le swap
```bash
swapon /dev/sdb3
```

### 4️⃣ Vérifier l’activation
```bash
lsblk
free -h
```

### 5️⃣ Montage Permanent du Swap
Ajouter dans `/etc/fstab` :
```
/dev/sdb3 none swap sw 0 0
```
**Ou avec UUID** :
```bash
blkid /dev/sdb3
```
Puis ajouter dans `/etc/fstab` :
```
UUID=xxxxx-xxxxx none swap sw 0 0
```

## 🎯 **Résumé des Commandes Principales**
## 🎯 **Résumé des Commandes Principales**
| Action | Commande |
|--------|---------|
| Afficher les périphériques blocs | `lsblk` |
| Création de partition MBR | `fdisk /dev/sdb` |
| Création de partition GPT | `gdisk /dev/sdb` ou `parted /dev/sdb` |
| Formater en ext4 | `mkfs.ext4 /dev/sdb1` |
| Monter temporairement | `mount /dev/sdb1 /mnt` |
| Monter en permanence | Modifier `/etc/fstab` |
| Voir UUID | `blkid /dev/sdb1` |
| Supprimer une partition | `fdisk /dev/sdb` puis `d` |
| Créer une partition swap | `fdisk /dev/sdb` puis `mkswap /dev/sdb3` |
| Activer le swap | `swapon /dev/sdb3` |


---
# 📦 **Gestion des Volumes Logiques (LVM)**
## 🔹 **Création d'un LVM**

### 1️⃣ Création des partitions :
```bash
fdisk /dev/sdb  
```
- Taper `n`, puis `Enter` (nouvelle partition)
- `Enter` trois fois, puis `+1G` (taille)
- Taper `t`, puis `Enter`, taper `8e` (ID LVM)
- Taper `w` pour sauvegarder

### 2️⃣ Création des volumes physiques :
```bash
pvcreate /dev/sdb1 /dev/sdc1
pvs  # Vérification
pvdisplay  # Détails des volumes physiques
```

### 3️⃣ Création d'un groupe de volumes :
```bash
vgcreate vg /dev/sdb1 /dev/sdc1
vgcreate vg /dev/sdb1 /dev/sdc1 -s 32M  # Taille PE
```

### 4️⃣ Création d'un volume logique :
```bash
lvcreate -n lv1 -L 70M vg
lvcreate -n lv1 -l 3 vg
lvdisplay  # Vérification
lvs  # Affichage des volumes logiques
```

### 5️⃣ Formatage du volume logique :
```bash
mkfs.xfs /dev/vg/lv1
```

### 6️⃣ Montage :
#### 🔹 Temporaire :
```bash
mount /dev/vg/lv1 /mnt
```

#### 🔹 Permanent :
```bash
vim /etc/fstab
```
Ajouter :
```bash
UUID=xxxxxx /mnt xfs defaults 0 0
```
Puis appliquer :
```bash
mount -a
```

## ❌ **Suppression d'un LVM**

### 1️⃣ Démontage :
```bash
umount /dev/vg/lv1
```

### 2️⃣ Suppression des volumes logiques :
```bash
lvremove /dev/vg/lv1
```

### 3️⃣ Suppression du groupe de volumes :
```bash
vgremove vg
```

### 4️⃣ Suppression des volumes physiques :
```bash
pvremove /dev/sdb1 /dev/sdc1
```

### 5️⃣ Suppression des partitions :
```bash
fdisk /dev/sdb
# Puis taper d, choisir la partition et w pour sauvegarder
```

## 🔹 **Augmentation et Réduction de la Taille d'un Volume Logique**
### 1️⃣ Augmenter la Taille d'un Volume Logique
#### 📌 Cas 1 : Espace libre disponible dans le VG
```bash
lvextend -L +100M /dev/vg/lv1 -r
lvextend -L 500M /dev/vg/lv1 -r
lvextend -L +25 /dev/vg/lv1 -r
lvextend -L 125 /dev/vg/lv1 -r
```
Sinon :
```bash
lvresize -L +100M /dev/vg/lv1 -r
```
Pour XFS :
```bash
xfs_growfs /dev/vg/lv1
```
Pour EXT4 :
```bash
resize2fs /dev/vg/lv1
```

#### 📌 Cas 2 : Pas d’espace libre dans le VG
1. Créer une nouvelle partition :
```bash
fdisk /dev/sdc
```
- `n` (nouvelle partition)
- `Enter` (valeurs par défaut)
- `Enter`
- `Enter`
- `+104M`
- `t` puis `8e`
- `w` pour sauvegarder

2. Créer un volume physique :
```bash
pvcreate /dev/sdc1
```
3. Étendre le groupe de volumes :
```bash
vgextend vg /dev/sdc1
```
4. Augmenter la taille du volume logique :
```bash
lvresize -L +100M /dev/vg/lv1 -r
```

### 2️⃣ Réduction de la Taille d’un Volume Logique
```bash
lvreduce -L -100M /dev/vg/lv1 -r
lvreduce -L 300M /dev/vg/lv1 -r
lvreduce -L -25 /dev/vg/lv1 -r
lvreduce -L 75 /dev/vg/lv1 -r
```

---

✅ **Fin du Guide sur la Gestion des Disques et des Volumes Logiques sous Linux** ✅

🚀 **Tout est maintenant bien organisé et optimisé !**
