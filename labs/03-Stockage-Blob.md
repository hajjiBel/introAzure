# Lab 03 - Créer un Stockage d'Objets Blob Azure

## Vue d'ensemble
Ce lab vous guide à travers la création d'un compte de stockage Azure, la configuration de conteneurs blob, l'upload de fichiers, et la surveillance de votre stockage.

---

## Objectifs Pédagogiques

À l'issue de ce lab, vous serez capable de :

- **Créer un compte de stockage** : Provisionner un compte Azure Storage avec les bonnes configurations
- **Configurer la redondance** : Comprendre et sélectionner la stratégie de redondance (LRS, GRS, etc.)
- **Gérer les conteneurs Blob** : Créer et configurer des conteneurs pour stocker les objets
- **Charger des fichiers** : Uploader des blobs et gérer les contrôles d'accès
- **Monitorer le stockage** : Surveiller les performances et la capacité du compte de stockage
- **Gérer les coûts** : Supprimer les ressources inutiles pour optimiser les dépenses

---


## Prérequis

- Un compte Azure actif
- Accès au Portail Azure
- Un fichier image ou document pour l'upload (JPG, PNG, PDF, etc.)

---

## Architecture et Ressources

```
┌───────────────────────────────────┐
│     Portail Azure                 │
└──────────────┬────────────────────┘
               │
               │ Crée
               ▼
┌───────────────────────────────────┐
│  Groupe de Ressources             │
│  StorageGroupeRessource           │
└──────────────┬────────────────────┘
               │
               │ Contient
               ▼
┌───────────────────────────────────┐
│  Compte de Stockage               │
│  - Type: Storage Account          │
│  - Redondance: LRS (Local)        │
│  - Performance: Standard          │
│  - Accès: Chaud                   │
└──────────────┬────────────────────┘
               │
               │ Contient
               ▼
┌───────────────────────────────────┐
│  Conteneur Blob                   │
│  - Nom: container1                │
│  - Accès: Privé (aucun anonyme)   │
└──────────────┬────────────────────┘
               │
               │ Contient
               ▼
┌───────────────────────────────────┐
│  Fichiers/Blobs                   │
│  - image.jpg                      │
│  - document.pdf                   │
└───────────────────────────────────┘
```

---

## Étapes de Réalisation

### Tâche 1 : Créer un Groupe de Ressources

1. Connectez-vous au **Portail Azure**
2. Recherchez **Groupes de ressources**
3. Cliquez sur **+ Créer**
4. Remplissez:
   - **Nom**: `StorageGroupeRessource`
   - **Région**: `(États-Unis) USA Est`
5. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 2 : Créer un Compte de Stockage

1. Recherchez **Comptes de stockage** dans le portail
2. Cliquez sur **+ Créer**
3. Dans l'onglet **Informations de base**, remplissez:
   - **Abonnement**: Valeur par défaut
   - **Groupe de ressources**: `StorageGroupeRessource`
   - **Nom du compte de stockage**: `storageaccountXXXXX` (remplacez XXXXX par des chiffres aléatoires pour garantir l'unicité mondiale)
   - **Région**: `(États-Unis) USA Est`
   - **Performance**: `Standard`
   - **Redondance**: `Stockage localement redondant (LRS)`

4. Cliquez sur **Examiner + créer**
5. Validez les paramètres et cliquez sur **Créer**
6. Attendez la notification de création réussie

---

### Tâche 3 : Naviguer vers le Compte de Stockage

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**
2. Ou recherchez votre compte de stockage dans **Comptes de stockage**
3. Vous êtes maintenant dans la page Vue d'ensemble de votre compte de stockage

---

### Tâche 4 : Créer un Conteneur Blob

1. Dans le menu gauche du compte de stockage, allez dans **Stockage de données** > **Conteneurs**
2. Cliquez sur **+ Conteneur** ou **Conteneur**
3. Remplissez les informations:
   - **Nom**: `container1`
   - **Niveau d'accès public**: `Privé (aucun accès anonyme)`
4. Cliquez sur **Créer**
5. Attendez que le conteneur apparaisse dans la liste

---

### Tâche 5 : Charger un Fichier dans le Blob

1. Cliquez sur le conteneur `container1`
2. Vous êtes maintenant à l'intérieur du conteneur (vide)
3. Cliquez sur **Charger** ou **+ Ajouter un blob**
4. Cliquez sur **Parcourir les fichiers** et sélectionnez une image ou un document (JPG, PNG, PDF, etc.)
5. Vous pouvez consulter les **Options avancées** pour voir:
   - Type de blob
   - Taille de bloc
   - Niveau d'accès
6. Cliquez sur **Charger** ou **Uploader**
7. Attendez la fin de l'upload (la barre de progression disparaît)

---

### Tâche 6 : Explorer le Fichier Chargé

1. Une fois le fichier chargé, il apparaît dans la liste du conteneur
2. Cliquez sur le fichier pour voir ses **Propriétés**
3. Vous verrez des options:
   - **Afficher/modifier** : Prévisualiser le contenu
   - **Télécharger** : Télécharger le fichier sur votre ordinateur
   - **Supprimer** : Supprimer le blob
   - **Générer l'URL SAS** : Créer une URL d'accès temporaire

4. Cliquez sur **Afficher/modifier** pour prévisualiser le fichier

---

### Tâche 7 : Charger Plusieurs Fichiers

1. Retournez à votre conteneur `container1`
2. Cliquez sur **Charger**
3. Vous pouvez sélectionner plusieurs fichiers à la fois
4. Cliquez sur **Dossier** pour charger un dossier entier
5. Chargez 2-3 fichiers supplémentaires
6. Vérifiez qu'ils apparaissent tous dans le conteneur

---

### Tâche 8 : Surveiller le Compte de Stockage

1. Retournez à la page principale de votre **compte de stockage**
2. Allez dans **Diagnostic et résolution de problèmes**
3. Explorez les problèmes courants listés
4. Allez dans **Supervision** > **Insights** ou **Métriques**
5. Vous verrez:
   - **Pannes** : Incidents de service
   - **Performances** : Latence et débit
   - **Disponibilité** : Uptime du service
   - **Capacité** : Espace utilisé vs alloué

---

### Tâche 9 : Explorer les Options de Stockage

1. Retournez à **Stockage de données** et explorez:
   - **Partages de fichiers** : Pour le stockage SMB/NFS
   - **Tables** : Pour les données NoSQL
   - **Files d'attente** : Pour la communication asynchrone

2. Ces services offrent des alternatives au stockage Blob pour d'autres cas d'usage

---

### Tâche 10 : Nettoyage des Ressources

1. Retournez à **Groupes de ressources**
2. Sélectionnez `StorageGroupeRessource`
3. Cliquez sur **Supprimer le groupe de ressources**
4. Tapez le nom exact du groupe pour confirmer
5. Cliquez sur **Supprimer**

---

## Résumé des Apprentissages

| Concept | Description |
|---------|-------------|
| **Compte de stockage** | Conteneur Azure pour tous les services de stockage |
| **Conteneur Blob** | Répertoire pour organiser les objets blob (comme un dossier) |
| **Blob** | Objet binaire large (fichier) stocké dans le cloud |
| **LRS (Redondance locale)** | Données répliquées 3 fois dans le même datacenter |
| **Niveau d'accès public** | Contrôle qui peut accéder aux blobs (privé vs public) |
| **Durabilité** | Garantie que vos données ne seront pas perdues |
| **Monitoring** | Surveillance des performances et de la disponibilité |

---

## Questions de Révision

1. Pourquoi choisir LRS au lieu de GRS ou RAGRS?
2. Quelle est la différence entre un conteneur et un blob?
3. Comment limiter l'accès à vos fichiers privés dans un blob?
4. Qu'est-ce qu'une URL SAS et quand l'utiliser?
5. Où trouvez-vous les métriques de performance de votre stockage?

---

## Dépannage Courant

| Problème | Solution |
|---------|----------|
| **Nom de compte non unique** | Ajoutez des chiffres aléatoires à la fin du nom |
| **Upload lent** | Vérifiez votre bande passante Internet |
| **Fichier non accessible** | Vérifiez que le conteneur n'est pas en accès "Privé" |
| **Coûts élevés** | Supprimez les conteneurs inutilisés et les vieux fichiers |

---

## Ressources Supplémentaires

- [Documentation Azure Blob Storage](https://learn.microsoft.com/fr-fr/azure/storage/blobs/)
- [Niveaux d'accès et redondance](https://learn.microsoft.com/fr-fr/azure/storage/common/storage-redundancy)
- [Sécurité et SAS tokens](https://learn.microsoft.com/fr-fr/azure/storage/common/storage-sas-overview)

---

**Dernière mise à jour**: Décembre 2025
