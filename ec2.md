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
