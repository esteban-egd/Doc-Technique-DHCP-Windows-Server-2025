# `🌐` ︲ Documentation TP : Installer et configurer un service DHCP

Ce dépôt présente un guide complet pour la mise en place et la configuration d'un service DHCP (Dynamic Host Configuration Protocol) sous Windows Server 2025. Il couvre l'étude du contexte, l'installation du rôle, la création d'étendues, de plages d'exclusion, de réservations, ainsi que des observations réseau via Wireshark. Tu apprendras à gérer l'adressage IP dynamique dans un environnement d'entreprise simulé.

---

<a id="sommaire"></a>
## 📑 ︲ Sommaire 

- [📘 ︲ Introduction](#introduction)
  - [❔ ︲ Contexte et objectifs](#contexte)
  - [🧰 ︲ Présentation des outils](#outils)
- [🧩 ︲ Mission 1 : Choisir un plan d'adressage et définir l'adresse IP du serveur](#mission1)
  - [🎯 ︲ Objectif](#m1-objectif)
  - [🛠️ ︲ Étape 1 : Étude du Contexte](#m1-etape1)
  - [🛠️ ︲ Étape 2 : Schéma Réseau](#m1-etape2)
  - [🛠️ ︲ Étape 3 : Configuration IP Statique](#m1-etape3)
- [🧩 ︲ Mission 2 : Installer le rôle DHCP](#mission2)
- [🧩 ︲ Mission 3 : Créer une Étendue DHCP](#mission3)
  - [🛠️ ︲ Procédure](#m3-proc)
  - [⚙️ ︲ Paramètres de l'étendue](#m3-param)
  - [🌍 ︲ Options DHCP](#m3-options)
  - [🔍 ︲ Configuration côté client](#m3-verif)
  - [📡 ︲ Observation Wireshark](#m3-wireshark)
- [🧩 ︲ Mission 4 : Créer une Plage d'Exclusion](#mission4)
- [🧩 ︲ Mission 5 : Créer des Réservations](#mission5)
- [🧩 ︲ Mission 6 : Basculement (Bonus)](#mission6)


---

<a id="introduction"></a>
# `📘` ︲ Introduction

---

<a id="contexte"></a>
## `❔` ︲ Contexte et objectifs du TP

> [!NOTE]
> **Ce TP simule la mise en place d'un service DHCP dans une entreprise fictive appelée Gal Cosmetic.** L'objectif est de maîtriser la gestion centralisée de l'adressage IP dynamique, en configurant un serveur DHCP sous Windows Server 2025, tout en observant les échanges réseau. Cela inclut la création d'étendues, de plages d'exclusion, de réservations, et une initiation aux mécanismes de redondance (basculement).

---

<a id="outils"></a>
## `🧰` ︲ Présentation des outils et prérequis

> [!IMPORTANT]
> **Présentation des outils et prérequis :**
>
> - `🖥️` ︱ **Serveur :** Windows Server 2025 
> - `💻` ︱ **Client :** Windows 11 
> - `🛠️` ︱ **Outil réseau :** Wireshark 
> - `🖼️` ︱ **Virtualisation :** VMware Workstation ou bien Hyper V

---

<a id="mission1"></a>
# `🧩` ︲ Mission 1 : Choisir un plan d'adressage et définir l'adresse IP du serveur

---

<a id="m1-objectif"></a>
## `🎯` ︲ Objectif

Définir un **plan d'adressage IP** adapté aux besoins de l'entreprise **Gal Cosmetic** et configurer une **adresse IP statique** sur le serveur Windows Server 2025.

---

<a id="m1-etape1"></a>
## `🛠️` ︲ Étape 1 : Étude du Contexte

### Équipements à prendre en compte

| Type d'équipement          | Quantité |
| -------------------------- | -------- |
| 💻 PC existants             | 85       |
| 💻 PC (nouvelles embauches) | 15       |
| 🗄️ Serveurs                 | 10       |
| 🖨️ Imprimantes              | 5        |
| **Total**                  | `115`    |

👉 Le réseau doit pouvoir fournir au minimum **115 adresses IP utilisables**, avec une marge d'évolution.

---

### Choix du plan d'adressage

Le réseau suivant est retenu :

| Paramètre                  | Valeur          |
| -------------------------- | --------------- |
| Classe                     | C               |
| Adresse réseau             | `192.168.0.0`   |
| Masque de sous-réseau      | `255.255.255.0` |
| CIDR                       | `/24`           |
| Nombre d'hôtes utilisables | 254             |

👉 Ce plan d'adressage permet de répondre aux besoins actuels et futurs de l'entreprise.

---

### Nombre d'hôtes 

- Bits disponibles pour les hôtes : **8**
- Nombre total d'adresses : **2⁸ = 256**
- Adresse réseau : `192.168.0.0`
- Adresse de broadcast : `192.168.0.255`

👉 **Nombre d'adresses IP utilisables : 254**

---

<a id="m1-etape2"></a>
## `🛠️` ︲ Étape 2 : Schéma Réseau 

Concevoir un schéma réseau représentant l'infrastructure globale de Gal Cosmetic :

![Texte alternatif](Capture/Schéma%20rzo-%201.png "Schema Réseau")

---

<a id="m1-etape3"></a>
## `🛠️` ︲ Étape 3 : Configuration de l'adresse IP statique du serveur

### `⚙️` ︲ Paramètres IP du serveur

| Paramètre  | Valeur          |
| ---------- | --------------- |
| Adresse IP | `192.168.0.1`   |
| Masque     | `255.255.255.0` |
| Passerelle | Non définie     |
| DNS        | Non défini      |

>[!IMPORTANT] 
>L'adresse IP est définie en statique afin d'assurer la **disponibilité permanente du serveur DHCP**.

---

### `📍` ︲ Accès à la configuration réseau

1. Ouvrir le **Gestionnaire de serveur**.
2. Aller dans **Serveur local**.
3. Cliquer sur Ethernet : `Adresse IPv4 attribuée par DHCP, Compatible IPv6`
4. Dans la page qui s'ouvre, faire **Clic Droit** sur la carte `Ethernet0`.
5. Cliquer sur **`Propriétés`** puis sur **`Protocole Internet version 4 (TCP/IPv4)`**
6. Choisir **`Utiliser l'adresse IP suivante :`**
7. Rentrer la configuration IP :
   - **Adresse IP :** `192.168.0.1`
   - **Masque de sous-réseau :** `255.255.255.0`

> [!TIP]
> Pour afficher les captures d'écrans, cliquez sur le menu déroulant avec l'émoji : 📸 

<details>
  <summary><strong>📸︲Configuration IP Statique</strong></summary>
  <img  src="Capture/Capture d’écran 2026-01-22 à 17.42.04.png"/>

  ---

  <img  src="Capture/Capture d’écran 2026-01-22 à 17.42.35.png"/>

  ---

  <img  src="Capture/Capture d’écran 2026-01-22 à 17.42.46.png"/>

  ---

  <img  src="Capture/Capture d’écran 2026-01-22 à 17.44.13.png"/>
</details>

---

<a id="mission2"></a>
# `🧩` ︲ Mission 2 : Installer le rôle DHCP

---

<a id="m2-objectif"></a>
## `🎯` ︲ Objectif

Installer le **rôle Serveur DHCP** sur Windows Server 2025.

---

<a id="m2-proc"></a>
## `🛠️` ︲ Procédure d'installation

1. Ouvrir le **Gestionnaire de serveur**.
2. Cliquer sur **`Gérer`**, puis sur **`Ajouter des rôles et fonctionnalités`**
3. Sélectionner **`Installation basée sur un rôle ou une fonctionnalité`**
4. Choisir le **serveur local**.
5. **Cocher** le rôle **`Serveur DHCP`**.
6. **Valider** l'ajout des fonctionnalités requises.
7. Laisser les **options par défaut**.
8. **Lancer l'installation**.

👉 Le rôle DHCP est maintenant installé sur le serveur.

<details>
  <summary><strong>📸︲Installation du rôle DHCP</strong></summary>
  <img  src="Capture/Capture d’écran 2026-02-08 à 11.56.07.png"/>

  ---

  <img  src="Capture/Capture d’écran 2026-02-08 à 12.08.07.png"/>

  ---

</details>


<a id="mission3"></a>
# `🧩` ︲ Mission 3 : Créer une Étendue DHCP


<a id="m3-objectif"></a>
## `🎯` ︲ Objectif

Créer une **étendue IPv4** permettant l'attribution automatique des adresses IP aux clients.

---

<a id="m3-proc"></a>
## `🛠️` ︲ Procédure de création de l'étendue

1. Dans le **Gestionnaire de serveur**.
2. Aller dans **`Outils`** puis **`DHCP`**.
3. **Déployer** le serveur grâce à la petite flèche `>` sur le côté gauche du nom du serveur.
4. **Clic droit** sur **`IPv4`** puis **`Nouvelle étendue`**.
5. **Suivre l'assistant** de création, en lui donant comme nom : `Réseau local`.
6. Pour la configuration, suivre [Paramètres de l'étendue](#️--paramètres-de-létendue)

<details>
  <summary><strong>📸︲Création de l'étendue</strong></summary>
  <img  src="Capture/Capture d’écran 2026-02-08 à 12.16.31.png"/>

  ---

  <img  src="Capture/Capture d’écran 2026-02-08 à 12.23.14.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 12.25.37.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 12.30.43.png"/>

</details>

---

<a id="m3-param"></a>
## `⚙️` ︲ Paramètres de l'étendue

| Paramètre | Valeur          |
| --------- | --------------- |
| Nom       | `Réseau local`  |
| Début     | `192.168.0.10`  |
| Fin       | `192.168.0.200` |
| Masque    | `255.255.255.0` |
| Bail      | Par défaut      |

<details>
  <summary><strong>📸︲Paramétrage de l'étendue</strong></summary>
  <img  src="Capture/Capture d’écran 2026-01-23 à 10.13.12.png"/>

  ---

  <img  src="Capture/Capture d’écran 2026-01-23 à 10.14.19.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-01-23 à 10.14.37.png"/>


</details>

---

<a id="m3-options"></a>
## `🌍` ︲ Options DHCP

| Option     | Valeur          |
| ---------- | --------------- |
| Passerelle | `192.168.0.254` |

👉 L'étendue est **activée** à la fin de l'assistant.

<details>
  <summary><strong>📸︲Passerelle DHP</strong></summary>
  <img  src="Capture/Capture d’écran 2026-02-08 à 12.42.46.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-01-23 à 10.16.30.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 13.04.23.png"/>

</details>

---

<a id="m3-verif"></a>
## `🔍` ︲ Configuration côté client

>[!IMPORTANT]
>**Configurer** la VM Windows 11 en **Réseau Interne** dans les parametre de VMware

1. Ouvrir une `Invite de commandes`.
2. Et exécuter :

```bash
ipconfig /all
```

👉 Le client reçoit une adresse IP dans l'étendue définie. **(Noter l'adresse du serveur DHCP).**

<details>
  <summary><strong>📸︲Adresse IP attribuée par le serveur DHCP</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 13.11.11.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 13.12.28.png"/>

</details>

<details>
  <summary><strong>📸︲Observations des baux, côtés serveur</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 13.18.54.png"/>

</details>

---

<a id="m3-wireshark"></a>
## `📡` ︲ Observation des échanges DHCP avec Wireshark

### Lancer Wireshark sur le client

1. **Télécharger** et **installer** Wireshark
2. **Lancer** Wireshark
3. **Sélectionner** la carte réseau (Ethernet), la capture démarrera automatiquement.

<details>
  <summary><strong>📸︲Selection Ethernet sur Wireshark</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 13.23.36.png"/>

</details>

---

### Reproduire une libération/renouvellement

1. Ouvrir une **Invite de commandes**
2. **Libérer** l'adresse IP :

```bash
ipconfig /release
```

3. **Renouveler** l'adresse IP :

```bash
ipconfig /renew
```

<details>
  <summary><strong>📸︲libération/renouvellement IP</strong></summary>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 13.25.18.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 13.26.05.png"/>

</details>

---

### Appliquer le filtre DHCP

1. Dans Wireshark, **arrêter** la capture
2. Dans la barre de filtre (en haut), saisir :

```
udp.port==68
```

3. Appuyer sur **Entrée**

**Résultat :** Affichage uniquement des paquets DHCP (port client = 68)

<details>
  <summary><strong>📸︲Filtre DHCP</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 13.28.35.png"/>

</details>

---

### Analyse des échanges DHCP

| Type de message   | Rôle                                  | IP source    | IP destination               |
| ----------------- | ------------------------------------- | ------------ | ---------------------------- |
| **DHCP DISCOVER** | Client demande une adresse IP         | 0.0.0.0      | 255.255.255.255  (broadcast) |
| **DHCP OFFER**    | Serveur propose une adresse IP        | 192.168.0.1  | 255.255.255.255  (broadcast) |
| **DHCP REQUEST**  | Client confirme le choix de l'adresse | 0.0.0.0      | 255.255.255.255  (broadcast) |
| **DHCP ACK**      | Serveur confirme l'attribution        | 192.168.0.1  | 255.255.255.255  (broadcast) |
| **DHCP RELEASE**  | Client libère son adresse IP          | 192.168.0.11 | 255.255.255.255  (broadcast) |

> [!NOTE]
> **Port UDP 68** = Port client DHCP  
> **Port UDP 67** = Port serveur DHCP

---

<a id="mission4"></a>
# `🧩` ︲ Mission 4 : Créer une Plage d'Exclusion

<a id="m4-objectif"></a>
## `🎯` ︲ Objectif

Empêcher l'attribution des adresses IP réservées aux **serveurs** (qui auront des adresses IP statiques).

---

<a id="m4-proc"></a>
## `🛠️` ︲ Procédure

1. Ouvrir la console `DHCP`, (dans le gestionnaire de serveur, `Outils` puis `DHCP`)
2. Aller dans `IPv4 → Étendue [192.168.0.0] Réseau local`.
3. `Clic droit` sur `Pool d'adresses` → `Nouvelle plage d'exclusion`.
4. Définir la plage suivante :

   - **Adresse de début :** `192.168.0.10`
   - **Adresse de fin :** `192.168.0.19`

5. Libérer l'adresse IP du client Windows 11, pour ceci tapez :
    ```bash
    ipconfig /release
    ```
6. Puis renouveler une adresse IP avec cette commande :
   ```bash
   ipconfig /renew
   ```

<details>
  <summary><strong>📸︲Plage d'exclusion</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 15.00.09 1.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 15.14.23.png"/>

</details>

👉 Ces adresses ne seront plus distribuées par le serveur DHCP.

<details>
  <summary><strong>📸︲Libérage IP / Renouvelage IP</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 15.27.35.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 15.28.21.png"/>

</details>

👉 Le poste client prendra une adresse IP en dehors de la plage d'exclusion que nous avons appliquée.

---

<a id="mission5"></a>
# `🧩` ︲ Mission 5 : Créer des Réservations d'Adresses

<a id="m5-objectif"></a>
## `🎯` ︲ Objectif

Attribuer des adresses IP **fixes** aux **imprimantes** via des réservations DHCP.

---

<a id="m5-proc"></a>
## `🛠️` ︲ Procédure de création des réservations

1. Ouvrir la console `DHCP`, (dans le gestionnaire de serveur, `Outils` puis `DHCP`).
2. Aller dans `IPv4 → Étendue [192.168.0.0] Réseau local`.
3. `Clic droit` → `Nouvelle réservation` → `Nouvelle réservation`
4. Renseigner l'adresse IP et l'adresse MAC correspondante, définie dans [Tableau des Réservations](#m5-tableau).

> [!IMPORTANT]
> **Les adresses MAC doivent être saisies SANS séparateurs** (ni `:` ni `-`)

<details>
  <summary><strong>📸︲Réservations IP</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 15.32.40.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 15.37.38.png"/>

</details>

---

<a id="m5-tableau"></a>
## `🖨️` ︲ Tableau des Réservations

| Imprimante | Service                | Adresse IP      | Adresse MAC    |
| ---------- | ---------------------- | --------------- | -------------- |
| 1          | Recherche & Dév        | `192.168.0.100` | `90004E87BF3C` |
| 2          | Design & Communication | `192.168.0.101` | `344DEA8975B2` |
| 3          | Marketing              | `192.168.0.102` | `268F3522A465` |
| 4          | Finances               | `192.168.0.103` | `00C0CA30EDD7` |
| 5          | Ressources Humaines    | `192.168.0.104` | `701A045F9B3B` |

---

<a id="mission6"></a>
# `🧩` ︲ Mission 6 : Créer un Basculement entre Deux Serveurs DHCP (Bonus)

<a id="m6-objectif"></a>
## `🎯` ︲ Objectif

Mettre en place une **redondance DHCP** en clonant le serveur principal. Si le celui ci est indisponible, le serveur secondaire prend automatiquement le relais.

---

<a id="m6-proc"></a>
## `🛠️` ︲ Procédure

### Étape 1 : Cloner le serveur DHCP principal

1. **Arrêter complètement** la VM serveur DHCP principale
2. **Cloner la machine virtuelle** :
   - Clic droit sur la VM → `Clone` ou `Exporter` (pour Hyper V)
   - **Réinitialiser l'adresse MAC** ou `Copier (génerer un nouvel ID)`
   - Nom du clone : `DHCP-Secondaire`
3. **Configurer la nouvelle VM** :
   - Réseau : **Même réseau interne** que le serveur principal

<details>
  <summary><strong>📸︲Clonage de la VM</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 15.58.57.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.00.13.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.05.53.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.06.20.png"/>

</details>

---

### Étape 2 : Modifier l'adresse IP du serveur secondaire

1. **Démarrer** le clone
2. **Modifier l'adresse IP statique** :
   - Ancienne : `192.168.0.1`
   - **Nouvelle : `192.168.0.2`** (ou `.3`)
   - Masque : **Toujours `255.255.255.0`**
3. **Appliquer** et **vérifier** :

```bash
ipconfig /all
```

<details>
  <summary><strong>📸︲Configuration IP serveur secondaire</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 16.15.01.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.15.20.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.15.42.png"/>

</details>

<details>
  <summary><strong>📸︲Vérification IP serveur secondaire</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 16.25.32.png"/>

</details>

---

### Étape 3 : Configurer le basculement

1. Ouvrir **sur le serveur DHCP PRINCIPAL** la console `DHCP`, (dans le gestionnaire de serveur, `Outils` puis `DHCP`)
2. Aller dans `IPv4` → `Clic droit` sur `Étendue [192.168.0.0] Réseau local`.
3. Cliquer sur `Configurer un basculement`.
4. Et configurer en laissant les options par defaut et en choisissant `dhcp2026` comme `secret partagé`.

<details>
  <summary><strong>📸︲Basculement</strong></summary>

  <img src="Capture/Capture d’écran 2026-02-08 à 16.29.15.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.29.51.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.38.27.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.39.44.png"/>

  ---

  <img src="Capture/Capture d’écran 2026-02-08 à 16.39.52.png"/>

</details>

---

### Étape 4 : Vérification du basculement

1. Sur le serveur **secondaire**, ouvrir la console **DHCP**
2. Vérifier que l'étendue **`Réseau local`** apparaît (répliquée)
3. **Arrêter** le serveur principal pour simuler une panne
4. Sur le client Windows 11 :
   - Libérer l'IP : 
   ```bash
   ipconfig /release
   ```
   - Renouveler l'IP :
   ```bash
   ipconfig /renew
   ```
5. **Vérifier** que le client reçoit toujours une adresse

```bash
ipconfig /all
```

**Résultat attendu :** IP attribuée + serveur DHCP = `192.168.0.2`

<details>
  <summary><strong>📸︲Basculement fonctionnel</strong></summary>

<img src="Capture/Capture d’écran 2026-02-08 à 16.58.44.png"/>

---

<img src="Capture/Capture d’écran 2026-02-08 à 17.05.48.png"/>

---

<img src="Capture/Capture d’écran 2026-02-08 à 17.21.38.png"/>
</details>

---

<a id="conclusion"></a>
# `✅` ︲ Missions réalisées


| Mission | Titre                                         | Statut      |
| ------- | --------------------------------------------- | ----------- |
| 1       | Plan d'adressage et configuration IP statique | ✅ Complétée |
| 2       | Installation du rôle DHCP                     | ✅ Complétée |
| 3       | Création d'une étendue DHCP                   | ✅ Complétée |
| 4       | Plage d'exclusion                             | ✅ Complétée |
| 5       | Réservations pour imprimantes                 | ✅ Complétée |
| 6       | Basculement DHCP (Bonus)                      | ✅ Complétée |

---

**Esteban GUILLERMIN EGIDIO** | **BTS SIO SISR**  


