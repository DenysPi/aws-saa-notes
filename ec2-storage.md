# EC2 - Stockage 


## EBS (Elastic Block Store)

EBS = un disque RÉSEAU que tu attaches à ton instance pendant qu'elle tourne.

### Caractéristiques
- C'est un disque réseau (pas physiquement dans la machine)
  - Communique avec l'instance via le réseau → un peu de latence possible
  - Peut être détaché d'une instance et rattaché à une autre rapidement
- Permet aux données de PERSISTER, même après la terminaison de l'instance(à condition que "Delete on termination" = No)
- Ne peut être monté que sur UNE seule instance à la fois
- Lié à une seule AZ
  - Pour le déplacer vers une autre AZ → il faut d'abord en faire un snapshot
- Capacité "provisionnée" (taille en Go + IOPS)
  - Tu paies pour toute la capacité provisionnée (utilisée ou non)
  - Tu peux augmenter la capacité au fil du temps

<img width="660" height="282" alt="image" src="https://github.com/user-attachments/assets/1835c0d5-ff75-41a6-a8d7-b9ba5e13f6cb" />

### Image mentale
Comme une clé USB amovible — mais un disque RÉSEAU, pas branché physiquement.

### EBS Snapshots

Un snapshot = une sauvegarde (photo) d'un volume EBS à un instant T.
On peut recréer un volume à partir d'un snapshot.

<img width="532" height="202" alt="image" src="https://github.com/user-attachments/assets/d297ff19-5a75-4896-ae84-c9c626c024b5" />

- Pas besoin de détacher le volume pour faire un snapshot, mais recommandé.
- Un snapshot peut être copié entre AZ et entre régions.
  → C'est LE moyen de déplacer un volume EBS hors de son AZ.

### Fonctionnalités

**Snapshot Archive**
- Range un snapshot dans un stockage 75 % moins cher (archivage long terme).
- Restauration lente : 24 à 72 h pour le sortir des archives.

<img width="213" height="132" alt="image" src="https://github.com/user-attachments/assets/8fbe5dde-c3e1-4a5f-b78f-2512195282e6" />

**Recycle Bin (corbeille)**
- Protège contre la suppression accidentelle.
- Rétention configurable : de 1 jour à 1 an.

<img width="213" height="125" alt="image" src="https://github.com/user-attachments/assets/6af3e039-4275-4b77-bea9-a30de31fee6d" />

**Fast Snapshot Restore (FSR)**
- Initialise complètement le volume à l'avance → zéro latence au premier accès.
- Coûte cher → réservé aux cas où la perf immédiate est critique.

### Types de volumes EBS

- **gp2 / gp3 (SSD)** = volume SSD polyvalent, équilibre prix/performance
  pour une grande variété de charges de travail
- **io1 / io2 Block Express (SSD)** = volume SSD haute performance, pour
  charges critiques nécessitant faible latence ou haut débit
- **st1 (HDD)** = volume HDD bon marché, pour charges fréquemment
  consultées et gourmandes en débit
- **sc1 (HDD)** = volume HDD le moins cher, pour charges peu fréquemment
  consultées

**Seuls gp2/gp3 et io1/io2 Block Express peuvent servir de boot volume**

### Cas d'usage par type

**SSD**
- Stockage économique, faible latence
- Volumes de démarrage système, postes virtuels, environnements de test
- Taille : 1 GiB - 16 TiB
- **gp3 :**
  - Base de 3000 IOPS et débit de 125 MiB/s
  - Peut augmenter jusqu'à 16 000 IOPS et 1000 MiB/s de débit
- **gp2 :**
  - Les petits volumes gp2 peuvent monter en rafale (burst) jusqu'à 3000 IOPS
  - Taille du volume et IOPS sont liés, max IOPS = 16 000
  - 3 IOPS par GiB → à 5334 GiB, on atteint déjà le max

**Provisioned IOPS (PIOPS) SSD**
- Applications critiques nécessitant des IOPS soutenus, ou besoin de
  plus de 16 000 IOPS
- Idéal pour les charges de bases de données
- Supporte EBS Multi-Attach (voir plus loin)

- **io1 (4 GiB - 16 TiB) :**
  - Max PIOPS = 64 000 pour les instances EC2 Nitro, 32 000 pour les autres
  - Peut augmenter les PIOPS indépendamment de la taille de stockage

- **io2 Block Express (4 GiB - 64 TiB) :**
  - Latence sous la milliseconde
  - Max PIOPS = 256 000, avec un ratio IOPS:GiB de 1000:1 (pour 1 GiB, on peut aller jusqu'à 1000 IOPS)

**HDD**
- Ne peuvent PAS servir de boot volume
- Taille : 125 GiB à 16 TiB

- **Throughput Optimized HDD (st1) :**
  - Big data, entrepôts de données, traitement de logs
  - Débit max 500 MiB/s — IOPS max 500

- **Cold HDD (sc1) :**
  - Pour données peu fréquemment consultées
  - Scénarios où le coût le plus bas prime
  - Débit max 250 MiB/s — IOPS max 250



## EC2 Instance Store

- Les volumes EBS sont des disques réseau avec de bonnes performances,
  mais limitées.
- Si tu as besoin d'un disque physique haute performance → EC2 Instance Store :
  - Meilleures performances I/O (le disque est directement sur la machine
    physique, pas via le réseau)
  - Les Instance Store PERDENT leurs données si l'instance est arrêtée (Stop)
  - Bon pour : buffer, cache, contenu temporaire
  - Risque de perte de données si le matériel tombe en panne
  - La sauvegarde est TA responsabilité (AWS ne la gère pas pour toi)

<img width="293" height="306" alt="image" src="https://github.com/user-attachments/assets/e4a1f1f7-eff1-47ef-9829-8cc8cf0d3f75" />

**OIPS** = le nombre de requêtes de lecture/écriture que le système de stockage peut traiter par seconde.

**Bandwidth** = est la quantité de données pouvant être transférée entre le stockage et le système pendant une unité de temps
