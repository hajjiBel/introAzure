# Lab 07 - Créer une Base de Données SQL Azure

## Vue d'ensemble
Ce lab vous guide à travers la création d'une base de données SQL Azure, la configuration des pare-feu, l'interrogation des données avec l'éditeur de requête, et la surveillance de la base de données.

---

## Objectifs Pédagogiques

À l'issue de ce lab, vous serez capable de :

- **Créer une base de données SQL** : Provisionner une instance SQL Database sur Azure
- **Configurer le serveur SQL** : Gérer l'authentification et les identifiants
- **Configurer les pare-feu** : Autoriser l'accès à la base de données depuis votre ordinateur
- **Interroger les données** : Utiliser l'éditeur de requête pour exécuter des commandes SQL
- **Charger des données** : Utiliser les données d'exemple (AdventureWorksLT)
- **Monitorer les performances** : Surveiller la base de données et les requêtes
- **Comprendre la tarification** : Gérer les coûts selon le niveau de service

---

## Prérequis

- Un compte Azure actif
- Accès au Portail Azure
- Connaissances en SQL (SELECT, JOIN, etc.)
- Aucun client SQL spécial requis (l'éditeur de requête est dans le portail)

---

## Architecture et Ressources

```
┌────────────────────────────────────┐
│        Portail Azure               │
└──────────────┬─────────────────────┘
               │
               ▼
┌────────────────────────────────────┐
│   Groupe de Ressources             │
│   SQLGroupeRessource               │
└──────────────┬─────────────────────┘
               │
               ▼
┌────────────────────────────────────┐
│   Serveur SQL Server               │
│ (sqlserverXXXX.database.windows.net)│
│   ├─ Authentification: sqluser     │
│   ├─ Région: USA Est               │
│   └─ Endpoint public: Activé       │
└──────────────┬─────────────────────┘
               │
         ┌─────┴──────────┐
         │                │
         ▼                ▼
┌──────────────────┐ ┌──────────────────┐
│ Base de Données  │ │ Règles Pare-feu  │
│ db1              │ │ ├─ IP client     │
│(AdventureWorksLT)│ │ └─ Services Azure│
│                  │ └──────────────────┘
│ Tables:          │
│ ├─ Product       │
│ ├─ Category      │
│ ├─ Customer      │
│ └─ Orders        │
└──────────────────┘
```

---

## Étapes de Réalisation

### Tâche 1 : Créer le Groupe de Ressources

1. Connectez-vous au **Portail Azure**
2. Allez à **Groupes de ressources** > **+ Créer**
3. Remplissez:
   - **Nom**: `SQLGroupeRessource`
   - **Région**: `(États-Unis) USA Est`
4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 2 : Créer la Base de Données SQL

1. Recherchez **Bases de données SQL** dans le portail
2. Cliquez sur **+ Créer**
3. Remplissez l'onglet **Informations de base**:
   - **Abonnement**: Valeur par défaut
   - **Groupe de ressources**: `SQLGroupeRessource`
   - **Nom de la base de données**: `db1`

4. Sous **Serveur**, cliquez sur **Créer nouveau**:
   - **Nom du serveur**: `sqlserverXXXX` (remplacez XXXX par des chiffres aléatoires, doit être unique mondialement)
   - **Emplacement**: `(États-Unis) USA Est`
   - **Authentification**: `Authentification SQL`
   - **Connexion de l'administrateur du serveur**: `sqluser`
   - **Mot de passe**: Créez un mot de passe fort (ex: `Pa$$w0rd1234`)
   - **Confirmer le mot de passe**: Retapez le mot de passe

5. Cliquez sur **OK** pour créer le serveur

6. Remplissez le reste du formulaire de base de données:
   - **Voulez-vous utiliser le pool élastique?**: `Non`
   - **Calcul + stockage**: Cliquez pour configurer (laissez par défaut pour commencer)

7. Allez dans l'onglet **Mise en réseau**:
   - **Méthode de connectivité**: `Point de terminaison public`
   - **Autoriser les services et ressources Azure à accéder à ce serveur**: `Oui`
   - **Ajouter l'adresse IP actuelle du client**: `Non` (vous l'ajouterez plus tard)

8. Allez dans l'onglet **Paramètres supplémentaires**:
   - **Utiliser les données existantes**: `Exemple` (pour charger AdventureWorksLT)

9. Cliquez sur **Examiner + créer** > **Créer**
10. Attendez le déploiement (2-5 minutes)

---

### Tâche 3 : Accéder à l'Éditeur de Requête

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**
2. Ou recherchez votre base de données `db1`
3. Vous êtes maintenant dans la page Vue d'ensemble de `db1`
4. Cliquez sur **Éditeur de requête (préversion)** dans le menu gauche

---

### Tâche 4 : Se Connecter à la Base de Données

1. Dans l'éditeur de requête, choisissez:
   - **Type d'autorisation**: `Authentification SQL Server`
   - **Connexion**: `sqluser`
   - **Mot de passe**: Le mot de passe que vous avez créé

2. Cliquez sur **OK** ou **Connexion**
3. **Vous recevrez une erreur de pare-feu** - c'est normal!

---

### Tâche 5 : Configurer le Pare-feu

1. Lisez l'erreur pour voir votre **adresse IP actuelle** (ex: 157.50.195.195)
2. Cliquez sur le lien **Définir le pare-feu du serveur** dans le message d'erreur
3. Ou allez manuellement:
   - Retournez à votre base de données
   - Cliquez sur **Vue d'ensemble**
   - Cherchez l'option **Définir le pare-feu du serveur** ou **Pare-feu et réseaux virtuels**

4. Vous êtes maintenant dans les paramètres de pare-feu du serveur
5. Cliquez sur **+ Ajouter une adresse IP de client**
6. Votre adresse IP actuelle devrait être pré-remplie
7. Cliquez sur **Enregistrer**
8. Attendez que la règle soit appliquée (1-2 minutes)

---

### Tâche 6 : Se Reconnecter via l'Éditeur de Requête

1. Retournez à **Éditeur de requête (préversion)**
2. Essayez de vous connecter à nouveau avec:
   - **Connexion**: `sqluser`
   - **Mot de passe**: Votre mot de passe

3. Cette fois, la connexion devrait réussir
4. Vous êtes maintenant dans l'éditeur de requête avec la base de données chargée

---

### Tâche 7 : Exécuter une Première Requête

1. Dans le panneau de l'éditeur, écrivez cette requête SQL:

```sql
SELECT TOP 20 
    pc.Name as CategoryName, 
    p.name as ProductName 
FROM SalesLT.ProductCategory pc 
JOIN SalesLT.Product p 
    ON pc.productcategoryid = p.productcategoryid;
```

2. Cliquez sur **Exécuter** ou appuyez sur `Ctrl+Shift+E`
3. Vous devriez voir les résultats dans le panneau inférieur:
   - Catégories de produits
   - Noms de produits

---

### Tâche 8 : Explorer les Données Disponibles

Exécutez ces requêtes pour explorer:

```sql
-- Voir toutes les tables
SELECT TABLE_NAME FROM INFORMATION_SCHEMA.TABLES;

-- Voir les clients
SELECT TOP 10 FirstName, LastName, EmailAddress FROM SalesLT.Customer;

-- Voir les commandes
SELECT TOP 10 OrderID, OrderDate, TotalDue FROM SalesLT.SalesOrderHeader;

-- Statistiques des ventes
SELECT 
    COUNT(*) as TotalOrders,
    SUM(TotalDue) as TotalRevenue,
    AVG(TotalDue) as AverageOrderValue
FROM SalesLT.SalesOrderHeader;
```

---

### Tâche 9 : Créer une Requête Personnalisée

Créez votre propre requête pour explorer les données:

```sql
SELECT 
    p.ProductID,
    p.Name,
    p.ListPrice,
    c.Name as Category,
    COUNT(soh.OrderID) as OrderCount
FROM SalesLT.Product p
LEFT JOIN SalesLT.ProductCategory c 
    ON p.ProductCategoryID = c.ProductCategoryID
LEFT JOIN SalesLT.SalesOrderDetail sod 
    ON p.ProductID = sod.ProductID
LEFT JOIN SalesLT.SalesOrderHeader soh 
    ON sod.SalesOrderID = soh.SalesOrderID
GROUP BY p.ProductID, p.Name, p.ListPrice, c.Name
ORDER BY OrderCount DESC;
```

---

### Tâche 10 : Monitorer la Base de Données

1. Retournez à la **Vue d'ensemble** de votre base de données
2. Cliquez sur **Métriques** ou **Insights** dans le menu
3. Vous verrez:
   - **Utilisation du DTU** (Database Transaction Units)
   - **Stockage utilisé**
   - **Connexions actives**
   - **Requêtes lentes**

---

### Tâche 11 : Configurer les Alertes (Optionnel)

Pour être notifié des problèmes:

1. Allez à **Alertes** > **+ Nouvelle règle d'alerte**
2. Créez une alerte pour:
   - **Métrique**: `Pourcentage DTU`
   - **Condition**: `> 80%`
   - **Action**: Envoyer une notification email

---

### Tâche 12 : Nettoyage

1. Retournez à **Groupes de ressources** > `SQLGroupeRessource`
2. Cliquez sur **Supprimer le groupe de ressources**
3. Confirmez et supprimez

⚠️ **Important** : Supprimez la base de données quand vous ne l'utilisez plus pour éviter des coûts Azure!

---

## Résumé des Apprentissages

| Concept | Description |
|---------|-------------|
| **Serveur SQL** | Conteneur cloud qui héberge plusieurs bases de données |
| **Base de données SQL** | Instance de données relationnelle complète |
| **Authentification SQL** | Connexion avec utilisateur/mot de passe (aussi disponible Azure AD) |
| **Pare-feu** | Contrôle qui peut accéder à votre serveur SQL (par IP) |
| **DTU** | Unité de transaction de base de données (mesure de performance) |
| **Éditeur de requête** | Outil web pour exécuter des requêtes SQL directement dans le portail |
| **AdventureWorksLT** | Base de données d'exemple avec données réalistes |

---

## Dépannage Courant

| Problème | Solution |
|---------|----------|
| **Erreur de pare-feu** | Ajoutez votre adresse IP aux règles de pare-feu |
| **Requête lente** | Vérifiez les index et utilisez EXPLAIN PLAN |
| **Coûts élevés** | Passez à un niveau inférieur ou supprimez les ressources inutilisées |
| **Impossible se connecter** | Vérifiez le nom d'utilisateur, mot de passe, et les paramètres de réseau |

---

## Ressources Supplémentaires

- [Documentation Azure SQL Database](https://learn.microsoft.com/fr-fr/azure/azure-sql/database/)
- [Sécurité SQL Database](https://learn.microsoft.com/fr-fr/azure/azure-sql/database/security-overview)
- [Performances et DTU](https://learn.microsoft.com/fr-fr/azure/azure-sql/database/service-tiers-dtu)
- [Sauvegarde et récupération](https://learn.microsoft.com/fr-fr/azure/azure-sql/database/business-continuity-high-availability-disaster-recovery-ha)

---

**Dernière mise à jour**: Décembre 2025
