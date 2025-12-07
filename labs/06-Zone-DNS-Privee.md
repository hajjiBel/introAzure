# Lab 06 - Créer une Zone Azure DNS Privée

## Vue d'ensemble
Ce lab vous montre comment créer une zone DNS privée, lier un réseau virtuel, et utiliser des enregistrements DNS personnalisés pour la résolution de noms interne dans Azure.

---

## Objectifs Pédagogiques

À l'issue de ce lab, vous serez capable de :

- **Créer une zone DNS privée** : Provisionner une zone DNS pour un domaine personnalisé
- **Lier une zone au réseau virtuel** : Permettre aux VMs d'utiliser cette zone
- **Activer l'enregistrement automatique** : Enregistrer automatiquement les VMs dans la zone
- **Créer des enregistrements DNS** : Ajouter des enregistrements A pointant vers des VMs
- **Tester la résolution DNS** : Valider que les noms de domaine se résolvent correctement
- **Gérer les enregistrements** : Créer et modifier des entrées DNS

---

## Prérequis

- Un compte Azure actif
- Accès au Portail Azure
- Connaissances en DNS et résolution de noms
- Client SSH pour les tests (nslookup, dig)

---

## Architecture et Ressources

```
┌────────────────────────────────┐
│   Portail Azure                │
└──────────────┬─────────────────┘
               │
               ▼
┌────────────────────────────────┐
│  Groupe de Ressources          │
│  DNSZone-GroupeRessource       │
└──────────────┬─────────────────┘
               │
      ┌────────┴────────┐
      │                 │
      ▼                 ▼
┌──────────────────┐ ┌──────────────────┐
│ Zone DNS Privée  │ │ Réseau Virtuel   │
│ private.contoso  │ │ MyVirtual-Net    │
│ .com             │ │ 10.3.0.0/16      │
│                  │ └──────────────────┘
│ Enregistrements: │        │
│ ├─ A: db         │        │
│ │  10.3.0.4      │   Liaison
│ │                │   (Auto-registration)
│ └─ MX, CNAME     │        │
└──────────────────┘        │
                           ▼
                  ┌──────────────────┐
                  │  MyVM1           │
                  │  10.3.0.4        │
                  │  db.private...   │
                  │  MyVM1.private.. │
                  └──────────────────┘
                           
                  ┌──────────────────┐
                  │  MyVM2           │
                  │  10.3.0.5        │
                  └──────────────────┘
```

---

## Étapes de Réalisation

### Tâche 1 : Créer le Groupe de Ressources

1. Connectez-vous au **Portail Azure**
2. Allez à **Groupes de ressources** > **+ Créer**
3. Remplissez:
   - **Nom**: `DNSZone-GroupeRessource`
   - **Région**: `France-Centre`
4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 2 : Créer la Zone DNS Privée

1. Recherchez **Zones DNS privées** dans le portail
2. Cliquez sur **+ Créer**
3. Remplissez le formulaire:
   - **Abonnement**: Valeur par défaut
   - **Groupe de ressources**: `DNSZone-GroupeRessource`
   - **Nom**: `private.contoso.com`
   - **Région**: Non applicable (zones DNS sont globales)

4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 3 : Créer le Réseau Virtuel

1. Recherchez **Réseaux virtuels** > **+ Créer**
2. Remplissez l'onglet **Informations de base**:
   - **Groupe de ressources**: `DNSZone-GroupeRessource`
   - **Nom**: `MyVirtual-Network`
   - **Région**: `France-Centre`

3. Allez dans l'onglet **Adresses IP**:
   - **Espace d'adresses IPv4**: `10.3.0.0/16`
   - **Nom du sous-réseau**: `Default`
   - **Espace d'adresses du sous-réseau**: `10.3.0.0/24`

4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 4 : Lier la Zone DNS au Réseau Virtuel

1. Allez à votre zone DNS `private.contoso.com`
2. Allez à **Paramètres** > **Liaisons de réseau virtuel**
3. Cliquez sur **+ Ajouter**
4. Remplissez:
   - **Nom de la liaison**: `MyVNet-Link`
   - **Réseau virtuel**: `MyVirtual-Network`
   - **Activer l'enregistrement automatique**: Cochez cette case

5. Cliquez sur **OK**
6. Vérifiez que la liaison a l'état **Réussie**

---

### Tâche 5 : Créer les Machines Virtuelles de Test

#### MyVM1:

1. Créez une VM **MyVM1**:
   - **Groupe de ressources**: `DNSZone-GroupeRessource`
   - **Réseau virtuel**: `MyVirtual-Network`
   - **Image**: `Ubuntu Server 22.04 LTS`
   - **Taille**: `B2s`
   - Configurez l'authentification SSH

#### MyVM2:

2. Créez une VM **MyVM2**:
   - Mêmes paramètres que MyVM1
   - Elle sera automatiquement enregistrée dans la zone DNS

---

### Tâche 6 : Créer un Enregistrement DNS Supplémentaire

1. Allez à votre zone DNS `private.contoso.com`
2. Cliquez sur **+ Jeu d'enregistrements** ou **Enregistrement**
3. Remplissez le formulaire:
   - **Nom**: `db` (l'enregistrement sera db.private.contoso.com)
   - **Type**: `A`
   - **Adresse IP**: Entrez l'**adresse IP privée de MyVM1** (10.3.0.4)
   - **TTL**: 3600 (ou par défaut)

4. Cliquez sur **Ajouter** ou **Créer**

---

### Tâche 7 : Vérifier les Enregistrements Automatiques

1. Retournez à la zone DNS
2. Vous devriez voir les enregistrements créés automatiquement:
   - `MyVM1.private.contoso.com` → 10.3.0.4
   - `MyVM2.private.contoso.com` → 10.3.0.5
   - `db.private.contoso.com` → 10.3.0.4 (créé manuellement)

---

### Tâche 8 : Tester la Résolution DNS depuis MyVM1

1. Connectez-vous à MyVM1 via SSH
2. Testez la résolution DNS:
   ```bash
   nslookup db.private.contoso.com
   nslookup MyVM2.private.contoso.com
   ```

3. Vous devriez obtenir les adresses IP privées correspondantes

---

### Tâche 9 : Tester la Résolution DNS depuis MyVM2

1. Connectez-vous à MyVM2 via SSH
2. Testez les résolutions:
   ```bash
   nslookup db.private.contoso.com
   nslookup MyVM1.private.contoso.com
   ```

---

### Tâche 10 : Configurer ICMP et Tester la Connectivité

Pour tester complètement, activez ICMP:

1. MyVM1 > NSG > Ajouter règle entrante ICMP
2. MyVM2 > NSG > Ajouter règle entrante ICMP

3. Depuis MyVM1:
   ```bash
   ping db.private.contoso.com
   ping MyVM2.private.contoso.com
   ```

---

### Tâche 11 : Ajouter un Enregistrement CNAME

Pour pratiquer:

1. Allez à la zone DNS
2. Cliquez sur **+ Jeu d'enregistrements**
3. Remplissez:
   - **Nom**: `webapp`
   - **Type**: `CNAME`
   - **Alias**: `MyVM1.private.contoso.com`

4. Testez: `nslookup webapp.private.contoso.com`

---

### Tâche 12 : Nettoyage

1. Allez à **Groupes de ressources** > `DNSZone-GroupeRessource`
2. Cliquez sur **Supprimer le groupe de ressources**
3. Confirmez et supprimez

---

## Résumé des Apprentissages

| Concept | Description |
|---------|-------------|
| **Zone DNS privée** | Zone DNS interne non accessible depuis Internet |
| **Liaison réseau virtuel** | Connexion entre une zone DNS et un VNet |
| **Enregistrement A** | Associe un nom de domaine à une adresse IPv4 |
| **Enregistrement CNAME** | Alias pointant vers un autre enregistrement |
| **Enregistrement automatique** | Les VMs sont enregistrées automatiquement dans la zone |
| **TTL (Time To Live)** | Durée en secondes avant rafraîchissement du cache |
| **nslookup** | Outil de diagnostic pour la résolution DNS |

---

## Questions de Révision

1. Quelle est la différence entre une zone DNS publique et privée?
2. Pourquoi avez-vous besoin d'une liaison réseau virtuel?
3. Comment les VMs sont-elles enregistrées automatiquement?
4. Que signifie un TTL de 3600?
5. Peut-on créer des enregistrements DNS manuellement en plus des automatiques?

---

## Dépannage Courant

| Problème | Solution |
|---------|----------|
| **nslookup échoue** | Vérifiez la liaison réseau virtuel et attendez quelques minutes |
| **Enregistrement non visible** | Actualisez la zone DNS ou attendez le TTL |
| **Ping échoue** | Configurez les règles ICMP dans les NSGs |

---

## Ressources Supplémentaires

- [Azure DNS Privée](https://learn.microsoft.com/fr-fr/azure/dns/private-dns-overview)
- [Enregistrements DNS](https://learn.microsoft.com/fr-fr/azure/dns/dns-zones-records)
- [Diagnostic DNS](https://learn.microsoft.com/fr-fr/azure/dns/dns-getstarted-portal)

---

**Dernière mise à jour**: Décembre 2025
