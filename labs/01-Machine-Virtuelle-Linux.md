# Lab 01 - Création d'une Machine Virtuelle Linux sous Azure

## Vue d'ensemble
Ce lab vous guide à travers la création et la configuration d'une machine virtuelle Linux sous Microsoft Azure, en utilisant le portail Azure pour provisionner les ressources et effectuer les tests de performance initiales.

---

## Objectifs Pédagogiques

À l'issue de ce lab, vous serez capable de :

- **Créer une ressource** : Provisionner une machine virtuelle Linux (Ubuntu) sur la plateforme Azure
- **Configurer l'authentification** : Mettre en place l'accès SSH et gérer les credentials
- **Gérer les ressources cloud** : Organiser les ressources au sein d'un groupe de ressources
- **Accéder à distance** : Établir une connexion SSH sécurisée vers la VM
- **Nettoyer les ressources** : Supprimer proprement les ressources pour éviter les coûts

---


## Prérequis

- Un compte Azure actif avec accès au portail Azure (https://portal.azure.com)
- Un client SSH installé (PuTTY, OpenSSH ou équivalent)
- Connaissances de base en ligne de commande Linux
- Accès à une connexion Internet stable

---

## Architecture et Ressources

```
┌─────────────────────────────────┐
│   Portail Azure                 │
│  (https://portal.azure.com)     │
└────────────┬────────────────────┘
             │
             │ Crée
             ▼
┌─────────────────────────────────┐
│  Groupe de Ressources           │
│  Formation[VotreNom]            │
└────────────┬────────────────────┘
             │
             │ Contient
             ▼
┌─────────────────────────────────┐
│  Machine Virtuelle Linux        │
│  - Nom: VM1                     │
│  - OS: Ubuntu 22.04             │
│  - Région: Europe du Nord       │
│  - Taille: B2S (2vCPU, 4 Go)    │
│  - Authentification: SSH/Clé    │
└─────────────────────────────────┘
```

---

## Étapes de Réalisation

### Tâche 1 : Créer le Groupe de Ressources

1. Connectez-vous au **Portail Azure** à l'adresse [https://portal.azure.com](https://portal.azure.com)
2. Cliquez sur **Groupes de ressources** dans le panneau gauche
3. Cliquez sur **+ Créer** ou **+ Ajouter**
4. Remplissez les informations suivantes:
   - **Nom du groupe de ressources**: `Formation[VotreNom]` (ex: FormationAlice)
   - **Région**: `Europe du Nord`
5. Cliquez sur **Examiner + créer** puis **Créer**
6. Attendez la notification de confirmation

---

### Tâche 2 : Créer la Machine Virtuelle

1. Dans le portail Azure, recherchez **Machines virtuelles** dans la barre de recherche
2. Cliquez sur **+ Créer** > **Machine virtuelle Azure**
3. Remplissez l'onglet **Informations de base** avec:
   - **Abonnement**: Conservez la valeur par défaut
   - **Groupe de ressources**: Sélectionnez `Formation[VotreNom]`
   - **Nom de la machine**: `VM1`
   - **Région**: `Europe du Nord`
   - **Image**: `Ubuntu Server 22.04 LTS - Gen2`
   - **Taille**: `B2s (2 vCPU, 8 Go de RAM)`

4. Configurez l'authentification:
   - **Type d'authentification**: `Clé publique SSH`
   - **Nom d'utilisateur**: `azureuser`
   - **Type de clé SSH**: `Générer une nouvelle paire de clés`
   - **Nom de la paire de clés**: `VM1_key`

5. Sous **Ports d'entrée publics**, sélectionnez: `SSH (22)`
6. Cliquez sur **Examiner + créer**
7. Validez les paramètres et cliquez sur **Créer**
8. **IMPORTANT** : Téléchargez et sauvegardez le fichier de clé privée (`VM1_key.pem`) dans un endroit sûr

---

### Tâche 3 : Obtenir l'Adresse IP Publique

1. Attendez que le déploiement soit terminé
2. Cliquez sur **Accéder à la ressource** ou recherchez votre VM `VM1` dans les machines virtuelles
3. Dans le panneau Vue d'ensemble, notez l'**adresse IP publique** (ex: 20.50.100.150)
4. Mémorisez cette adresse pour la connexion SSH

---

### Tâche 4 : Connecter la Machine Virtuelle via SSH

**Option A : Avec OpenSSH (Linux/Mac)**

1. Ouvrez un terminal
2. Modifiez les permissions de la clé privée:
   ```bash
   chmod 600 VM1_key.pem
   ```

3. Connectez-vous à la VM:
   ```bash
   ssh -i VM1_key.pem azureuser@<ADRESSE_IP_PUBLIQUE>
   ```

4. Acceptez l'empreinte du serveur en tapant `yes`

**Option B : Avec PuTTY (Windows)**

1. Ouvrez PuTTYgen
2. Cliquez sur **File** > **Load private key** et sélectionnez `VM1_key.pem`
3. Cliquez sur **Save private key** et sauvegardez le fichier en `.ppk`
4. Ouvrez PuTTY:
   - **Host Name**: `azureuser@<ADRESSE_IP_PUBLIQUE>`
   - **Connection** > **SSH** > **Auth**: Sélectionnez le fichier `.ppk`
5. Cliquez sur **Open**

---

### Tâche 5 : Mettre à Jour le Système d'Exploitation

Une fois connecté, mettez à jour tous les packages:

```bash
sudo apt-get update
sudo apt-get upgrade -y
```

Vérifiez la version Ubuntu:
```bash
lsb_release -a
```

---

### Tâche 6 : Vérifier les Ressources Système

Consultez les informations système via ces commandes:

```bash
# Afficher la mémoire disponible
free -h

# Afficher le CPU
lscpu

# Afficher l'espace disque
df -h

# Afficher l'utilisation système en temps réel
top
```

---

### Tâche 7 : Nettoyage des Ressources

Pour éviter les coûts Azure inutiles:

1. Retournez au **Portail Azure**
2. Recherchez **Groupes de ressources**
3. Sélectionnez `Formation[VotreNom]`
4. Cliquez sur **Supprimer le groupe de ressources**
5. Confirmez en tapant le nom du groupe de ressources
6. Cliquez sur **Supprimer**

---

## Résumé des Apprentissages

| Concept | Description |
|---------|-------------|
| **Groupe de ressources** | Conteneur logique pour organiser toutes vos ressources Azure |
| **Image VM** | Template préconfigurée du système d'exploitation (Ubuntu 22.04 LTS) |
| **Taille VM** | Configuration hardware allouée à votre machine (2 vCPU, 4 Go RAM pour B2s) |
| **Authentification SSH** | Mécanisme sécurisé d'accès sans mot de passe |
| **Adresse IP publique** | Adresse IPv4 permettant l'accès de l'extérieur vers votre VM |
| **Benchmark de performance** | Mesure quantitative des capacités de calcul de votre système |

---

## Questions de Révision

1. Quel est le rôle du groupe de ressources dans Azure?
2. Pourquoi utiliser l'authentification SSH plutôt que les mots de passe?
3. Qu'est-ce que la taille B2s et quelles sont ses spécifications?
4. Comment accéder à une VM Azure depuis votre machine locale?
5. Quelles sont les étapes pour supprimer complètement une VM et ses ressources associées?

---

## Dépannage Courant

| Problème | Solution |
|---------|----------|
| **Erreur de permission avec la clé SSH** | Utilisez `chmod 600 VM1_key.pem` pour corriger les permissions |
| **Timeout de connexion SSH** | Vérifiez que le groupe de sécurité réseau autorise le port 22 (SSH) |
| **Adresse IP publique non visible** | Attendez 2-3 minutes après la création de la VM |
| **Coûts élevés** | Supprimez la VM quand vous ne l'utilisez pas |

---

**Dernière mise à jour**: Décembre 2025
