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
