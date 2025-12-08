# Lab 05 - Connexion VNet-to-VNet avec Passerelle

## Vue d'ensemble
Ce lab vous montre comment connecter deux réseaux virtuels situés dans la même région en utilisant des passerelles de réseau virtuel et une connexion VNet-to-VNet, offrant un contrôle et une sécurité plus avancés qu'avec le peering simple.

---

## Objectifs Pédagogiques

À l'issue de ce lab, vous serez capable de :

- **Créer des passerelles VNet** : Provisionner des gateways pour les connexions sécurisées
- **Configurer les connexions** : Établir des connexions VNet-to-VNet chiffrées
- **Gérer les protocoles IKE** : Comprendre le chiffrement IKEv2
- **Déployer des machines virtuelles** : Créer des ressources de test sur chaque VNet
- **Tester la connectivité sécurisée** : Valider la communication chiffrée
- **Dépanner les gateways** : Identifier et corriger les problèmes de connexion

---

## Prérequis

- Un compte Azure actif
- Accès au Portail Azure
- Connaissances en réseautage et chiffrement IPSec
- Patience pour le déploiement (20+ minutes pour les gateways)

---

## Architecture et Ressources

```
┌──────────────────────────────────────────┐
│           Portail Azure                  │
└──────────────────────────────────────────┘
            │                      │
            ▼                      ▼
┌─────────────────────┐   ┌─────────────────────┐
│ VNet-N1             │   │ VNet-N2             │
│ 192.168.1.0/24      │   │ 10.10.0.0/16        │
│  └─ Default         │   │  └─ Default         │
│     192.168.1.0/26  │   │     10.10.0.0/24    │
│  └─ GatewaySubnet   │   │  └─ GatewaySubnet   │
│     192.168.1.64/26 │   │     10.10.1.0/24    │
└────────┬────────────┘   └────────┬────────────┘
         │                         │
         │ Contient                │ Contient
         ▼                         ▼
┌──────────────────────┐  ┌──────────────────────┐
│ VNet Gateway-N1      │  │ VNet Gateway-N2      │
│ (Virtual Network     │  │ (Virtual Network     │
│  Gateway)            │  │  Gateway)            │
│ Type: VPN            │  │ Type: VPN            │
│ SKU: Basic/Standard  │  │ SKU: Basic/Standard  │
└────────┬─────────────┘  └────────┬─────────────┘
         │                         │
         │ Connexion IKEv2         │
         │◄─────────────────────────►│
         │ (Bidirectionnelle)       │
         │
         ├─ MyVM1                   ├─ MyVM2
         │ 192.168.1.4               10.10.0.4
         │
         └─ Communication sécurisée via tunnel IPSec
```

---

## Étapes de Réalisation

### Tâche 1 : Créer le Groupe de Ressources

1. Connectez-vous au **Portail Azure**
2. Allez à **Groupes de ressources** > **+ Créer**
3. Remplissez:
   - **Nom**: `VnetToVnet-GroupeRessource`
   - **Région**: `France-Centre`
4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 2 : Créer le Premier Réseau Virtuel

1. Recherchez **Réseaux virtuels** > **+ Créer**
2. Remplissez l'onglet **Informations de base**:
   - **Groupe de ressources**: `VnetToVnet-GroupeRessource`
   - **Nom**: `Virtual-Network-N1`
   - **Région**: `France-Centre`

3. Allez dans l'onglet **Adresses IP**:
   - **Espace d'adresses IPv4**: `192.168.1.0/24`
   - Cliquez sur le sous-réseau par défaut et modifiez:
     - **Nom**: `Default`
     - **Espace d'adresses**: `192.168.1.0/26`
   - Cliquez sur **+ Ajouter un sous-réseau**:
     - **Nom**: `GatewaySubnet`
     - **Espace d'adresses**: `192.168.1.64/26`

4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 3 : Créer le Deuxième Réseau Virtuel

1. Allez à **Réseaux virtuels** > **+ Créer**
2. Remplissez l'onglet **Informations de base**:
   - **Groupe de ressources**: `VnetToVnet-GroupeRessource`
   - **Nom**: `Virtual-Network-N2`
   - **Région**: `France-Centre`

3. Allez dans l'onglet **Adresses IP**:
   - **Espace d'adresses IPv4**: `10.10.0.0/16`
   - Modifiez le sous-réseau par défaut:
     - **Nom**: `Default`
     - **Espace d'adresses**: `10.10.0.0/24`
   - Cliquez sur **+ Ajouter un sous-réseau**:
     - **Nom**: `GatewaySubnet`
     - **Espace d'adresses**: `10.10.1.0/24`

4. Cliquez sur **Examiner + créer** > **Créer**

---

### Tâche 4 : Créer la Première Passerelle VNet (VNet-Gateway-N1)

1. Recherchez **Passerelles de réseau virtuel** > **+ Créer**
2. Remplissez le formulaire:
   - **Nom**: `VNet-Gateway-N1`
   - **Type de passerelle**: `VPN`
   - **Type de VPN**: `Basé sur itinéraire (Route-based)`
   - **Réseau virtuel**: `Virtual-Network-N1`
   - **Adresse IP publique**: Créez une nouvelle
     - **Nom**: `VNet-Gateway-N1-IP`
   - **Références (SKU)**: `Basic` ou `Standard`

3. Cliquez sur **Examiner + créer** > **Créer**
4. **Attendez la fin du déploiement** (très important!)

---

### Tâche 5 : Créer la Deuxième Passerelle VNet (VNet-Gateway-N2)

1. Allez à **Passerelles de réseau virtuel** > **+ Créer**
2. Remplissez le formulaire:
   - **Nom**: `VNet-Gateway-N2`
   - **Type de passerelle**: `VPN`
   - **Type de VPN**: `Basé sur itinéraire (Route-based)`
   - **Réseau virtuel**: `Virtual-Network-N2`
   - **Adresse IP publique**: Créez une nouvelle
     - **Nom**: `VNet-Gateway-N2-IP`
   - **Références (SKU)**: `Basic` ou `Standard`

3. Cliquez sur **Examiner + créer** > **Créer**
4. **Attendez la fin du déploiement**

---

### Tâche 6 : Créer la Connexion depuis VNet-Gateway-N1

1. Allez à **Passerelles de réseau virtuel** et sélectionnez `VNet-Gateway-N1`
2. Allez à **Paramètres** > **Connexions**
3. Cliquez sur **+ Ajouter**
4. Remplissez le formulaire:
   - **Nom**: `Connection-N1-to-N2`
   - **Type de connexion**: `VNet-to-VNet`
   - **Première passerelle de réseau virtuel**: `VNet-Gateway-N1` (pré-rempli)
   - **Deuxième passerelle de réseau virtuel**: `VNet-Gateway-N2`
   - **Clé partagée (PSK)**: `AZERTY123456789`
   - **Protocole IKE**: `IKEv2`

5. Cliquez sur **OK** ou **Ajouter**

---

### Tâche 7 : Créer la Connexion depuis VNet-Gateway-N2

1. Allez à **Passerelles de réseau virtuel** et sélectionnez `VNet-Gateway-N2`
2. Allez à **Paramètres** > **Connexions**
3. Cliquez sur **+ Ajouter**
4. Remplissez le formulaire:
   - **Nom**: `Connection-N2-to-N1`
   - **Type de connexion**: `VNet-to-VNet`
   - **Première passerelle de réseau virtuel**: `VNet-Gateway-N2` (pré-rempli)
   - **Deuxième passerelle de réseau virtuel**: `VNet-Gateway-N1`
   - **Clé partagée (PSK)**: `AZERTY123456789` (DOIT être la même!)
   - **Protocole IKE**: `IKEv2`

5. Cliquez sur **OK** ou **Ajouter**
6. Attendez que les deux connexions passent à l'état **Connecté**

---

### Tâche 8 : Créer la Machine Virtuelle MyVM1

1. Créez une VM **MyVM1**:
   - **Groupe de ressources**: `VnetToVnet-GroupeRessource`
   - **Réseau virtuel**: `Virtual-Network-N1`
   - **Sous-réseau**: `Default` (pas GatewaySubnet!)
   - **Image**: `Ubuntu Server 22.04 LTS`
   - **Taille**: `B2s`

2. Configurez l'authentification SSH et téléchargez la clé

---

### Tâche 9 : Créer la Machine Virtuelle MyVM2

1. Créez une VM **MyVM2**:
   - **Groupe de ressources**: `VnetToVnet-GroupeRessource`
   - **Réseau virtuel**: `Virtual-Network-N2`
   - **Sous-réseau**: `Default`
   - **Image**: `Ubuntu Server 22.04 LTS`
   - **Taille**: `B2s`

2. Configurez l'authentification SSH et téléchargez la clé

---

### Tâche 10 : Configurer les Groupes de Sécurité Réseau

Pour chaque VM, autorisez ICMP:

1. MyVM1 > NSG > Ajouter une règle entrante:
   - **Source**: `10.10.0.0/16`
   - **Protocole**: `ICMP`
   - **Action**: `Autoriser`

2. MyVM2 > NSG > Ajouter une règle entrante:
   - **Source**: `192.168.1.0/24`
   - **Protocole**: `ICMP`
   - **Action**: `Autoriser`

---

### Tâche 11 : Tester la Connectivité

1. Connectez-vous à MyVM1 via SSH
2. Pingez MyVM2 (obtenez son IP privée du portail):
   ```bash
   ping 10.10.0.4
   ```

3. Vérifiez que le ping fonctionne (réponses reçues)

---

### Tâche 12 : Vérifier l'État des Connexions

1. Allez à `VNet-Gateway-N1` > **Connexions**
2. Vérifiez que `Connection-N1-to-N2` a l'état **Connecté**
3. Allez à `VNet-Gateway-N2` > **Connexions**
4. Vérifiez que `Connection-N2-to-N1` a l'état **Connecté**

---

### Tâche 13 : Nettoyage

1. Allez à **Groupes de ressources** > `VnetToVnet-GroupeRessource`
2. Cliquez sur **Supprimer le groupe de ressources**
3. Confirmez et cliquez sur **Supprimer**

---

## Résumé des Apprentissages

| Concept | Description |
|---------|-------------|
| **Passerelle VNet** | Ressource qui permet les connexions VPN et ExpressRoute |
| **VNet-to-VNet** | Connexion sécurisée entre deux réseaux virtuels |
| **IKEv2** | Protocole de négociation de clés pour IPSec |
| **PSK (Clé partagée)** | Clé secrète utilisée pour authentifier les deux côtés |
| **GatewaySubnet** | Sous-réseau spécial réservé à la passerelle VPN |
| **SKU de passerelle** | Type et performance de la passerelle (Basic, Standard, HighPerformance) |
| **Tunnel IPSec** | Connexion chiffrée entre les deux gateways |

---


## Dépannage Courant

| Problème | Solution |
|---------|----------|
| **Déploiement lent des gateways** | C'est normal, attendez 20-30 minutes |
| **Connexion non établie** | Vérifiez la PSK, les adresses IP, et les NSGs |
| **Latence élevée** | Utilisez une SKU Standard ou HighPerformance |
| **Coûts élevés** | Les gateways sont facturées même si elles ne sont pas utilisées |

---

## Ressources Supplémentaires

- [Configuration VNet-to-VNet](https://learn.microsoft.com/fr-fr/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)
- [Passerelles VPN Azure](https://learn.microsoft.com/fr-fr/azure/vpn-gateway/vpn-gateway-about-vpngateways)
- [IPSec et IKEv2](https://learn.microsoft.com/fr-fr/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

---

**Dernière mise à jour**: Décembre 2025
