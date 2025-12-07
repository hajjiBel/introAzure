# Lab 02 - Création d'une Machine Virtuelle Windows sous Azure

## Vue d'ensemble
Ce lab vous guide à travers la création d'une machine virtuelle Windows 10 sur Azure, sa configuration, l'évaluation de ses performances, et les opérations de gestion réseau.

---

## Objectifs Pédagogiques

À l'issue de ce lab, vous serez capable de :

- **Provisionner une VM Windows** : Créer une machine virtuelle avec système d'exploitation Windows 10
- **Gérer l'authentification** : Configurer l'accès RDP avec des credentials sécurisés
- **Configurer le réseau** : Modifier les paramètres d'adresse IP et DNS
- **Évaluer les performances** : Utiliser des outils comme GeekBench pour mesurer le CPU et mémoire
- **Migrer les ressources** : Déplacer une VM vers un nouveau groupe de ressources
- **Redimensionner une VM** : Modifier la taille d'une machine selon ses besoins
- **Gérer le cycle de vie** : Comprendre les opérations de démarrage, arrêt et suppression

---

## Prérequis

- Un compte Azure actif avec accès au portail Azure
- Un client Bureau à Distance (Remote Desktop Client) - intégré dans Windows
- Connaissances de base en administration Windows
- Bande passante suffisante pour le protocole RDP

---

## Architecture et Ressources

```
┌────────────────────────────────────────┐
│     Portail Azure                      │
│  (https://portal.azure.com)            │
└──────────────┬─────────────────────────┘
               │
               │ Crée initialement
               ▼
┌────────────────────────────────────────┐
│  Groupe de Ressources (Initial)        │
│  Formation[VotreNom]                   │
└──────────────┬─────────────────────────┘
               │
               │ Contient
               ▼
┌────────────────────────────────────────┐
│  Machine Virtuelle Windows             │
│  - Nom: VM1                            │
│  - OS: Windows 10 Pro                  │
│  - Région: Europe du Nord              │
│  - Taille: B2S (2vCPU, 4 Go)          │
│  - Authentification: RDP (Port 3389)   │
│  - Adresse IP privée: x.x.x.150       │
│  - DNS: 8.8.8.8 / 8.8.4.4             │
└────────────────────────────────────────┘
               │
               │ Migre vers
               ▼
┌────────────────────────────────────────┐
│  Groupe de Ressources (V2)             │
│  Formation[VotreNom]-V2                │
└────────────────────────────────────────┘
```

---

## Étapes de Réalisation

### Tâche 1 : Créer le Groupe de Ressources Initial

1. Connectez-vous au **Portail Azure**
2. Allez dans **Groupes de ressources** > **+ Créer**
3. Remplissez:
   - **Nom**: `Formation[VotreNom]`
   - **Région**: `Europe du Nord`
4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 2 : Créer la Machine Virtuelle Windows

1. Recherchez **Machines virtuelles** dans le portail
2. Cliquez sur **+ Créer** > **Machine virtuelle Azure**
3. Configurez l'onglet **Informations de base**:
   - **Abonnement**: Valeur par défaut
   - **Groupe de ressources**: `Formation[VotreNom]`
   - **Nom de la machine**: `VM1`
   - **Région**: `Europe du Nord`
   - **Image**: `Windows 10 Pro, version 22H2 - Gen2`
   - **Taille**: `B2s (2 vCPU, 4 Go RAM)`

4. Configurez les identifiants:
   - **Nom d'utilisateur**: `m2iuser`
   - **Mot de passe**: Créez un mot de passe fort (> 12 caractères avec majuscules, minuscules, chiffres, symboles)
   - **Confirmer le mot de passe**: Retapez le mot de passe

5. Sous **Règles de port entrant public**:
   - Sélectionnez `RDP (3389)` et `SSH (22)` - optionnel pour SSH

6. Acceptez les conditions de licence Windows
7. Cliquez sur **Examiner + créer** > **Créer**
8. Attendez la notification de déploiement terminé

---

### Tâche 3 : Se Connecter à la Machine Virtuelle

1. Allez à la page de la VM déployée
2. Notez l'**adresse IP publique** (ex: 20.50.100.150)
3. Cliquez sur **Connexion** > **Bureau à distance**
4. Cliquez sur **Télécharger le fichier RDP**
5. Ouvrez le fichier `.rdp` téléchargé
6. Cliquez sur **Connexion**
7. Entrez vos identifiants:
   - **Utilisateur**: `m2iuser`
   - **Mot de passe**: Le mot de passe que vous avez créé
8. Acceptez les certificats de sécurité
9. Vous êtes maintenant connecté à votre VM Windows

---

### Tâche 4 : Modifier l'Adresse IP Privée

1. Retournez au **Portail Azure**
2. Recherchez votre VM `VM1`
3. Allez dans **Paramètres** > **Mise en réseau**
4. Cliquez sur l'**Interface réseau** (généralement nommée `VM1-nic`)
5. Allez dans **Paramètres** > **Configurations IP**
6. Cliquez sur la configuration IP (généralement `ipconfig1`)
7. Changez l'**Allocation** de `Dynamique` à `Statique`
8. Modifiez l'**adresse IP privée** pour que la dernière partie soit `.150`
   - Exemple: Si l'adresse actuelle est `10.0.0.4`, changez-la en `10.0.0.150`
9. Cliquez sur **Enregistrer**
10. Attendez 1-2 minutes pour que les modifications prennent effet

---

### Tâche 5 : Configurer les Serveurs DNS

1. Depuis le **Portail Azure**, allez dans **Paramètres** > **Mise en réseau**
2. Cliquez sur l'interface réseau
3. Allez dans **Paramètres** > **Serveurs DNS**
4. Sélectionnez **Personnalisé**
5. Entrez les serveurs DNS:
   - **Serveur DNS primaire**: `8.8.8.8`
   - **Serveur DNS secondaire**: `8.8.4.4`
6. Cliquez sur **Enregistrer**
7. Redémarrez la VM pour appliquer les modifications

---

### Tâche 6 : Créer un Nouveau Groupe de Ressources (V2)

1. Retournez à **Groupes de ressources** dans le portail
2. Cliquez sur **+ Créer**
3. Remplissez:
   - **Nom**: `Formation[VotreNom]-V2`
   - **Région**: `Europe du Nord`
4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 7 : Migrer la VM vers le Nouveau Groupe de Ressources

1. Accédez à la VM `VM1` dans votre groupe de ressources initial
2. Cliquez sur **Voir plus** (trois points) > **Déplacer**
3. Sélectionnez **Déplacer vers un autre groupe de ressources**
4. Choisissez le groupe de ressources cible: `Formation[VotreNom]-V2`
5. Cochez **Je comprends que les outils et références associées à ces ressources seront inutilisables jusqu'à ce que je les mette à jour**
6. Cliquez sur **OK**
7. Attendez que la migration soit complète

---

### Tâche 8 : Redimensionner la Machine Virtuelle

1. Allez à la VM `VM1` dans le groupe de ressources `Formation[VotreNom]-V2`
2. Arrêtez la VM en cliquant sur **Arrêter** (cela est nécessaire avant le redimensionnement)
3. Attendez que l'état change à "Arrêtée (désallouée)"
4. Cliquez sur **Paramètres** > **Taille**
5. Cherchez et sélectionnez `B2ms (2 vCPU, 8 Go RAM)`
6. Cliquez sur **Redimensionner**
7. Attendez la fin de l'opération
8. Redémarrez la VM

---

### Tâche 9 : Vérifier les Modifications

1. Connectez-vous à nouveau en Bureau à Distance
2. Vérifiez que le système fonctionne correctement
3. Ouvrez l'Explorateur de fichiers et vérifiez que vous disposez d'une plus grande capacité mémoire
4. Vous pouvez vérifier dans les Paramètres Système > À propos: vous devriez voir 8 Go RAM au lieu de 4 Go

---

### Tâche 10 : Nettoyage des Ressources

Pour éviter les coûts supplémentaires:

1. Allez à **Groupes de ressources**
2. Sélectionnez `Formation[VotreNom]-V2`
3. Cliquez sur **Supprimer le groupe de ressources**
4. Tapez le nom exact du groupe pour confirmer
5. Cliquez sur **Supprimer**

---

## Résumé des Apprentissages

| Concept | Description |
|---------|-------------|
| **Groupe de ressources** | Conteneur pour organiser et gérer les ressources Azure |
| **VM Windows** | Machine virtuelle avec système d'exploitation Windows intégré |
| **Authentification RDP** | Protocole de Bureau à distance pour l'accès graphique |
| **Adresse IP statique** | Configuration d'une IP privée fixe plutôt que dynamique |
| **Serveurs DNS** | Configuration des serveurs de résolution de noms personnalisés |
| **Redimensionnement VM** | Modification de la capacité matérielle (CPU, RAM) |
| **Migration de ressources** | Déplacement de ressources entre groupes de ressources |

---

## Questions de Révision

1. Pourquoi faut-il arrêter une VM avant de la redimensionner?
2. Quelle est la différence entre une adresse IP dynamique et statique?
3. Quel protocole utilise-t-on pour accéder graphiquement à une VM Windows?
4. Pourquoi changer les serveurs DNS de 8.8.8.8 et 8.8.4.4?
5. Comment migrer une VM vers un autre groupe de ressources sans la recréer?

---

## Dépannage Courant

| Problème | Solution |
|---------|----------|
| **Connexion RDP refusée** | Vérifiez que le groupe de sécurité réseau autorise le port 3389 |
| **Mot de passe oublié** | Utilisez la réinitialisation du mot de passe dans le portail |
| **Redimensionnement échoue** | Arrêtez complètement la VM avant le redimensionnement |
| **Serveurs DNS non appliqués** | Redémarrez la VM après modification des DNS |

---

## Ressources Supplémentaires

- [Documentation Azure VMs Windows](https://learn.microsoft.com/fr-fr/azure/virtual-machines/windows/)
- [Connexion Bureau à distance](https://learn.microsoft.com/fr-fr/azure/virtual-machines/windows/connect-logon)
- [Redimensionnement des VMs](https://learn.microsoft.com/fr-fr/azure/virtual-machines/windows/resize-vm)

---

**Dernière mise à jour**: Décembre 2025
