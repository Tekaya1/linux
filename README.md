# Gestion des Périphériques Blocs et Partitions sous Linux

## 1. Affichage des Périphériques Blocs
Afficher des renseignements sur les périphériques blocs disponibles :
```sh
lsblk
```

---

## 2. Création des Partitions

### 2.1. Disque MBR :
Utilisation de `fdisk` pour créer une partition sur un disque au format MBR :
```sh
fdisk /dev/sdb  # Exemple avec /dev/sdb
```

### 2.2. Disque GPT :
Utilisation de `gdisk` pour créer une partition sur un disque au format GPT :
```sh
gdisk /dev/sdb
```
ou avec `parted`, qui fonctionne pour MBR et GPT :
```sh
parted /dev/sdb
```

---

## 3. Configuration d'un Système de Fichiers

Un système de fichiers doit être formaté après la création de la partition.

### 3.1. Types de Systèmes de Fichiers
- **Windows** : NTFS, FAT32
- **Linux** : ext2, ext3, ext4

### 3.2. Création d'un Système de Fichiers ext4 :
```sh
mkfs.ext4 /dev/sdb1
mkfs -t ext4 /dev/sdb1
```

---

## 4. Montage des Partitions

### 4.1. Montage Temporaire :
Monter une partition sur `/mnt` :
```sh
mount /dev/sdb1 /mnt
```
Ou créer un répertoire spécifique et le monter :
```sh
mkdir /rep
mount /dev/sdb1 /rep
```

### 4.2. Montage Permanent :
Pour monter une partition automatiquement au démarrage, il faut modifier le fichier `/etc/fstab`.

1. **Éditer le fichier `/etc/fstab`** :
```sh
vim /etc/fstab
```
2. **Ajouter la ligne suivante** :
```
/dev/sdb1 /rep ext4 defaults 0 0
```
   - `/rep` : Point de montage
   - `ext4` : Type de système de fichiers

3. **Utilisation de l'UUID** (recommandé pour éviter les erreurs si le nom du périphérique change) :
```sh
blkid /dev/sdb1
```
Ajouter cette ligne dans `/etc/fstab` :
```
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /rep ext4 defaults 0 0
```
(Remplace `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` par l'UUID obtenu)

---

## 5. Suppression d'une Partition

### 5.1. Démontage de la partition :
Avant suppression, il faut démonter la partition :
```sh
umount /dev/sdb1
umount /rep
```

### 5.2. Suppression avec `fdisk` :
1. Ouvrir `fdisk` :
```sh
fdisk /dev/sdb
```
2. Supprimer la partition :
   - Appuyer sur `d` pour supprimer une partition.
   - Choisir la partition à supprimer.
   - Appuyer sur `w` pour enregistrer les modifications et quitter.

---
## 6. Creation d'une partition swap:
1. Lancer la commande :
```sh
fdisk /dev/sdb
```
 


---

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

---
