# EC2

## Bases
1. EC2 = Elastic Compute Cloud = de l'Infrastructure as a Service (IaaS)
2. Consiste principalement à savoir :
   - Louer une machine virtuelle (EC2)
   - Stocker les données sur des disques virtuels (EBS)
   - Répartir la charge entre plusieurs machines (ELB)
   - Faire du scaling automatique via un groupe (ASG)

**EC2 User Data script se lance qu une seule fois à l'initiliasation d'instance et il marche ave root user**

## EC2 Istance Types

AWS propose différentes "familles" d'instances, chacune optimisée pour un
besoin précis. On choisit selon ce qui compte le plus : le CPU, RAM,
ou le stockage.

### General Purpose (usage général)
- Équilibré entre CPU, mémoire et réseau.
- Pour des charges variées sans besoin extrême : serveurs web, dépôts de code.
- Le choix par défaut quand rien ne domine.

### Compute Optimized (optimisé calcul)
- Beaucoup de puissance CPU.
- Pour les tâches gourmandes en calcul :
  - traitement par lots (batch)
  - encodage / transcodage vidéo (media transcoding)
  - serveurs web à haute performance
  - calcul haute performance (HPC)
  - serveurs de jeux dédiés

### Memory Optimized (optimisé mémoire)
- Beaucoup de RAM.
- Pour traiter de gros volumes de données directement en mémoire :
  - bases de données haute performance
  - caches / bases de données en mémoire (in-memory)
  - traitement en temps réel de grandes quantités de données

### Storage Optimized (optimisé stockage)
- Accès disque local très rapide en lecture/écriture séquentielle.
- Pour les tâches très gourmandes en accès disque :
  - transactions en ligne à haute fréquence (OLTP)
  - bases de données relationnelles et NoSQL
  - systèmes de fichiers distribués

## Security groupes
C'est un paquet de règles qui décide quel trafic réseau a le droit d'entrer et de sortir d'une instance EC2
<img width="616" height="147" alt="image" src="https://github.com/user-attachments/assets/dfe813d0-39a5-45f8-b87a-a18fc113960d" />

- Les règles peuvent référencer soit une **IP** (ou une plage d'IP), soit un
  **autre security group**.
- Ils ne contiennent QUE des règles **Allow**
- Ce qu'ils contrôlent :
    - l'accès à certains **ports**
    - les plages d'**IP autorisées**
    - le trafic **entrant (inbound)** : des autres machines VERS l'instance
    - le trafic **sortant (outbound)** : de l'instance VERS l'extérieur
- **La bonne pratique est de créer un securuty group distinct pour SSH access**
<img width="905" height="462" alt="image" src="https://github.com/user-attachments/assets/39c0d84c-df94-4c4f-a380-35d57eef1f44" />

## Les portes classiques à connaitre
- 22 = SSH — se connecter à une instance Linux 
- 21 = FTP — téléverser des fichiers vers un partage de fichiers
- 22 = SFTP — téléverser des fichiers de façon sécurisée (via SSH)
- 80 = HTTP — accéder à des sites web non sécurisés
- 443 = HTTPS — accéder à des sites web sécurisés
- 3389 = RDP — se connecter à une instance Windows

**Dans presque 100% des cas quand la page ne veut pas se charger et fait timeout, c'est la problème de security groups**

## Connexion à EC2(SSH)

`ssh -i ssh.pem ec2-user@public-ip`

## EC2 Instances options

- **On-Demand (à la demande)**
   - Tu paies ce que tu consommes :
      - Linux/Windows → facturé à la seconde
      - Autres OS → facturé à l'heure
   - Coût le plus élevé, mais aucun paiement à l'avance
   - Aucun engagement de durée
   - Idéal pour des charges courtes et imprévisibles

- **Reserved Instances (réservées, 1 ou 3 ans)**
   - Tu t'engages sur 1 ou 3 ans → gros rabais (jusqu'à ~72 %)
   - Reserved Instances = charges longues et stables
   - Convertible Reserved = charges longues, mais tu peux changer le type d'instance (plus souple, rabais un peu moindre)
   - Idéal pour un usage constant et prévisible
   - Peuvent s'acheter/revendre sur le Reserved Instance Marketplace

- **Savings Plans (1 ou 3 ans)**
   - Engagement sur un MONTANT de dépense (ex : 10 $/heure) plutôt que sur une instance précise
   - Lié à une famille d'instances et une région
   - Plus souple que les Reserved sur le type exact d'instance
   
- **Spot Instances**
   - Instances très bon marché (jusqu'à ~90 % de rabais) MAIS AWS peut te les reprendre à tout moment si le prix du marché dépasse ton prix max
   - Idéal pour des charges qui tolèrent l'interruption (batch, traitements que l'on peut relancer)

- **Dedicated Hosts (hôtes dédiés)**
   - Un serveur physique entier réservé rien que pour toi
   - Sert à répondre à des contraintes de conformité, ou à réutiliser
     tes licences logicielles liées au matériel (par socket / cœur / VM)
   - Achat : On-Demand ou Reserved
   
- **Dedicated Instances (instances dédiées)**
   - Instances tournant sur du matériel dédié à ton compte
   - Peuvent partager le matériel avec d'autres instances DE TON compte
   - Pas de contrôle sur le placement physique

- **Capacity Reservations (réservation de capacité)**
   - Tu réserves de la capacité On-Demand dans une AZ précise, pour la durée que tu veux
   - Tu es sûr d'avoir la capacité quand tu en as besoin
   - Aucun engagement de durée, mais AUCUN rabais
   - Idéal pour une charge courte qui doit tourner dans une AZ précise

<img width="352" height="411" alt="image" src="https://github.com/user-attachments/assets/90e66f5e-611b-418f-be8c-75d6588e5d68" />
<img width="851" height="418" alt="image" src="https://github.com/user-attachments/assets/5b7536d1-1337-431a-8a29-13a89c70b675" />


## Spot Instances & Spot Feet

<img width="932" height="453" alt="image" src="https://github.com/user-attachments/assets/5f50c5e0-c7a2-43d3-a699-1f5555c3df38" />

### Types de requête Spot
- **One-Time (unique)** — si l'instance est interrompue, aucune nouvelle tentative.
  La requête est terminée, point.
- **Persistent (persistante)** — si l'instance est interrompue, AWS continue de
  réessayer : dès que de la capacité redevient disponible à ton prix, une nouvelle
  instance est relancée automatiquement.

### États d'une requête Spot
`open → active → (disabled ou closed ou cancelled ou failed)`

- `open` — en attente de capacité (la requête cherche à se placer)
- `active` — l'instance tourne
- `failed` — aucune capacité trouvée à ton prix max
- `cancelled` — tu as annulé la requête
- `disabled` — persistante uniquement : en pause temporaire
- `closed` — persistante uniquement : terminée définitivement

### Règles importantes
- On ne peut **annuler** une requête que si elle est dans l'état
  `open`, `active` ou `disabled`.
- **Annuler la requête ≠ terminer l'instance** → l'instance continue de tourner
  (et d'être facturée !) tant que tu ne la termines pas manuellement.
- Nettoyage complet = 2 étapes :
  1. Annuler la requête Spot (pour ne pas qu'elle en relance une nouvelle)
  2. Terminer l'instance elle-même

### Spot Fleets
Un ensemble de plusieurs Spot Instances que AWS lance et gère automatiquement selon une strategie

**Pourquoi l'utiliser**
- **Diversification** = si un type d'instance ou une AZ manque de capacité Spot, AWS bascule ver un autre type/AZ dispo, moins de rique d'interruption totale
- **Maintien automatique de capacité** = si une instance est interrompue, Spot Fleet en relance une autre ailleurs pour compenser
- Spot Fleet arrete de lancer des instances quans la capacité ou max depense est atteint

**Strategies d'allocation**
- **lowesPrice** = priorise le prix le plud bas
- **diversified** = répartit sur plusiers pools pour reduire le risque d'interruption
- **capacityOptimized** = priorise les pools avec le moins de risque d'interruption
- **priceCapacityOptimized(recommended)** = priorise les pools avec la meilleure capacité d'etre libre, puis choisir le pool moins cher
