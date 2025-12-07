# Lab 04 - Connexion de Réseaux Virtuels avec Peering

## Vue d'ensemble
Ce lab vous guide à travers la création de deux réseaux virtuels séparés et leur connexion via VNet Peering, permettant une communication sécurisée entre les ressources.

---

## Objectifs Pédagogiques

À l'issue de ce lab, vous serez capable de :

- **Créer des réseaux virtuels** : Provisionner plusieurs VNets avec configurations IP différentes
- **Définir les sous-réseaux** : Créer et organiser les espaces d'adressage
- **Configurer le peering** : Établir une liaison VNet-to-VNet
- **Déployer des ressources** : Créer des machines virtuelles sur des VNets différents
- **Tester la connectivité** : Valider la communication entre VMs
- **Gérer les règles de pare-feu** : Configurer les groupes de sécurité réseau
- **Dépanner la connectivité** : Identifier et corriger les problèmes de réseau

---

## Durée estimée
**45-60 minutes**

---

## Prérequis

- Un compte Azure actif
- Accès au Portail Azure
- Connaissances en adressage IP et réseautage
- Client SSH ou Bureau à distance installé

---

## Architecture et Ressources

```
┌─────────────────────────────────────────────────────┐
│              Portail Azure                          │
└──────────────────┬──────────────────────────────────┘
                   │
         ┌─────────┴─────────┐
         │                   │
         ▼                   ▼
┌─────────────────────┐  ┌─────────────────────┐
│  Groupe Ressources  │  │  Groupe Ressources  │
│Peering-GR           │  │Peering-GR           │
└─────────────────────┘  └─────────────────────┘
         │                   │
         │ Contient          │ Contient
         │                   │
         ▼                   ▼
┌──────────────────────┐ ┌──────────────────────┐
│ VNet-N1              │ │ VNet-N2              │
│ 10.1.0.0/16          │ │ 10.2.0.0/16          │
│  └─ Subnet: 10.1.0.0 │ │  └─ Subnet: 10.2.0.0 │
│     /24              │ │     /24              │
└──────────────────────┘ └──────────────────────┘
         │                   │
         │ Peering <────────►│
         │   (bidirectionnel)
         │
         ├─ MyVM1            ├─ MyVM2
         │  10.1.0.4          10.2.0.4
         │
         └─ Communication via ICMP (Ping)
```

---

## Étapes de Réalisation

### Tâche 1 : Créer le Groupe de Ressources

1. Connectez-vous au **Portail Azure**
2. Allez dans **Groupes de ressources** > **+ Créer**
3. Remplissez:
   - **Nom**: `Peering-GroupeRessource`
   - **Région**: `France-Centre`
4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 2 : Créer le Premier Réseau Virtuel (VNet-N1)

1. Recherchez **Réseaux virtuels** dans le portail
2. Cliquez sur **+ Créer**
3. Remplissez l'onglet **Informations de base**:
   - **Abonnement**: Valeur par défaut
   - **Groupe de ressources**: `Peering-GroupeRessource`
   - **Nom**: `Virtual-Network-N1`
   - **Région**: `France-Centre`

4. Allez dans l'onglet **Adresses IP**:
   - **Espace d'adresses IPv4**: `10.1.0.0/16`
   - Cliquez sur **+ Ajouter un sous-réseau** si nécessaire
   - **Nom du sous-réseau**: `Default`
   - **Espace d'adresses du sous-réseau**: `10.1.0.0/24`

5. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 3 : Créer le Deuxième Réseau Virtuel (VNet-N2)

1. Allez à **Réseaux virtuels** > **+ Créer**
2. Remplissez l'onglet **Informations de base**:
   - **Abonnement**: Valeur par défaut
   - **Groupe de ressources**: `Peering-GroupeRessource`
   - **Nom**: `Virtual-Network-N2`
   - **Région**: `France-Centre`

3. Allez dans l'onglet **Adresses IP**:
   - **Espace d'adresses IPv4**: `10.2.0.0/16`
   - **Nom du sous-réseau**: `Default`
   - **Espace d'adresses du sous-réseau**: `10.2.0.0/24`

4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 4 : Configurer le Peering VNet-to-VNet

#### Première Direction (VNet-N1 → VNet-N2)

1. Allez à **Réseaux virtuels** et sélectionnez `Virtual-Network-N1`
2. Dans le menu gauche, allez à **Paramètres** > **Peerings**
3. Cliquez sur **+ Ajouter**
4. Remplissez le formulaire:
   - **Nom du peering**: `Peering-N1-N2`
   - **Réseau virtuel**: `Virtual-Network-N2`
   - **Nom du peering distant**: `Peering-N2-N1`
   - **Autoriser le trafic acheminé**: Cochez cette case
   - **Autoriser l'accès à la passerelle**: Cochez cette case

5. Cliquez sur **Ajouter**

#### Deuxième Direction (VNet-N2 → VNet-N1)

Le peering est généralement **bidirectionnel**, mais vérifiez:

1. Allez à **Virtual-Network-N2**
2. Allez à **Paramètres** > **Peerings**
3. Vérifiez que `Peering-N2-N1` existe et a l'état **Connecté**

---

### Tâche 5 : Créer la Première Machine Virtuelle (MyVM1)

1. Recherchez **Machines virtuelles** > **+ Créer**
2. Remplissez l'onglet **Informations de base**:
   - **Abonnement**: Valeur par défaut
   - **Groupe de ressources**: `Peering-GroupeRessource`
   - **Nom**: `MyVM1`
   - **Région**: `France-Centre`
   - **Image**: `Ubuntu Server 22.04 LTS`
   - **Taille**: `B2s`

3. Configurez l'authentification:
   - **Type d'authentification**: `Clé publique SSH`
   - **Nom d'utilisateur**: `azureuser`
   - **Clé publique SSH**: Générez une nouvelle paire
   - **Nom de la paire de clés**: `MyVM1_key`

4. Allez dans l'onglet **Mise en réseau**:
   - **Réseau virtuel**: `Virtual-Network-N1`
   - **Sous-réseau**: `Default (10.1.0.0/24)`
   - **Adresse IP publique**: Laissez l'option par défaut

5. Cliquez sur **Examiner + créer** > **Créer**
6. **Téléchargez et sauvegardez** la clé privée

---

### Tâche 6 : Créer la Deuxième Machine Virtuelle (MyVM2)

1. Allez à **Machines virtuelles** > **+ Créer**
2. Remplissez l'onglet **Informations de base**:
   - **Groupe de ressources**: `Peering-GroupeRessource`
   - **Nom**: `MyVM2`
   - **Région**: `France-Centre`
   - **Image**: `Ubuntu Server 22.04 LTS`
   - **Taille**: `B2s`

3. Configurez l'authentification:
   - **Type d'authentification**: `Clé publique SSH`
   - **Nom d'utilisateur**: `azureuser`
   - **Clé publique SSH**: Générez une nouvelle paire
   - **Nom de la paire de clés**: `MyVM2_key`

4. Allez dans l'onglet **Mise en réseau**:
   - **Réseau virtuel**: `Virtual-Network-N2`
   - **Sous-réseau**: `Default (10.2.0.0/24)`

5. Cliquez sur **Examiner + créer** > **Créer**
6. **Téléchargez et sauvegardez** la clé privée

---

### Tâche 7 : Configurer les Groupes de Sécurité Réseau (NSG)

Pour que le ping (ICMP) fonctionne, vous devez autoriser le trafic ICMP:

#### Pour MyVM1:

1. Allez à **MyVM1** > **Paramètres** > **Mise en réseau**
2. Cliquez sur le **groupe de sécurité réseau**
3. Allez à **Paramètres** > **Règles de sécurité entrantes**
4. Cliquez sur **+ Ajouter**
5. Remplissez:
   - **Source**: `Adresse IP` / `10.2.0.0/16` (plage du VNet-N2)
   - **Protocole**: `ICMP`
   - **Action**: `Autoriser`
   - **Priorité**: `100`
   - **Nom**: `AllowICMPFromN2`

6. Cliquez sur **Ajouter**

#### Pour MyVM2:

Répétez les mêmes étapes mais:
- **Source**: `10.1.0.0/16` (plage du VNet-N1)
- **Nom**: `AllowICMPFromN1`

---

### Tâche 8 : Tester la Connectivité

#### Test depuis MyVM1:

1. Obtenez l'**adresse IP publique** de MyVM1
2. Connectez-vous via SSH:
   ```bash
   ssh -i MyVM1_key.pem azureuser@<IP_PUBLIQUE_VM1>
   ```

3. Obtenir l'**adresse IP privée** de MyVM2 (depuis le portail Azure: 10.2.0.4)
4. Testez le ping:
   ```bash
   ping 10.2.0.4
   ```

5. Vous devriez voir des réponses ICMP réussies

#### Test depuis MyVM2:

1. Connectez-vous à MyVM2
2. Testez le ping vers MyVM1:
   ```bash
   ping 10.1.0.4
   ```

---

### Tâche 9 : Vérifier l'État du Peering

1. Allez à **Virtual-Network-N1** > **Paramètres** > **Peerings**
2. Vérifiez que le peering a l'état **Connecté**
3. Notez les détails:
   - Bande passante disponible
   - État de la connexion
   - Adresses distantes

---

### Tâche 10 : Nettoyage des Ressources

1. Allez à **Groupes de ressources**
2. Sélectionnez `Peering-GroupeRessource`
3. Cliquez sur **Supprimer le groupe de ressources**
4. Confirmez en tapant le nom exact
5. Cliquez sur **Supprimer**

---

## Résumé des Apprentissages

| Concept | Description |
|---------|-------------|
| **Réseau virtuel (VNet)** | Réseau isolé dans Azure où vivent vos ressources |
| **Sous-réseau** | Subdivisions d'un VNet pour organiser le trafic |
| **Peering VNet** | Connexion directe entre deux VNets sans gateway |
| **Espace d'adresses** | Plage CIDR allouée à un VNet (ex: 10.1.0.0/16) |
| **ICMP** | Protocole Internet Control Message (utilisé par ping) |
| **Groupe de sécurité réseau (NSG)** | Pare-feu cloud qui contrôle le trafic entrant/sortant |
| **Latence de peering** | Communication avec la même latence qu'un réseau local |

---

## Questions de Révision

1. Quelle est la différence entre le peering VNet et une connexion VPN?
2. Pourquoi faut-il configurer les groupes de sécurité réseau pour ICMP?
3. Comment vérifier que deux VNets sont correctement appairés?
4. Que se passe-t-il si vous changez l'espace d'adresses après le peering?
5. Peut-on avoir un peering entre VNets dans des régions différentes?

---

## Dépannage Courant

| Problème | Solution |
|---------|----------|
| **Ping échoue** | Vérifiez les règles NSG pour ICMP et les espaces d'adresses |
| **Peering non connecté** | Vérifiez que les deux VNets existent et ont des adresses différentes |
| **Latence élevée** | Utilisez une connexion ExpressRoute pour une performance optimale |
| **Coûts élevés** | Supprimez les VMs et les VNets inutilisés |

---

## Ressources Supplémentaires

- [Documentation VNet Peering](https://learn.microsoft.com/fr-fr/azure/virtual-network/virtual-network-peering-overview)
- [Groupes de sécurité réseau](https://learn.microsoft.com/fr-fr/azure/virtual-network/network-security-groups-overview)
- [Adressage IP Azure](https://learn.microsoft.com/fr-fr/azure/virtual-network/private-ip-addresses)

---

**Dernière mise à jour**: Décembre 2025
