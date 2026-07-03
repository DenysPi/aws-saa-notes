# EC2 Instance Store

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
