# AMI

AMI = Amazon Machine Image, un template qui sert à créer l'instance EC2.

AMI est lié à une région(peut etre copié a travers régions)

## Ce que contient une AMI

1. Un (ou plusieurs) snapshot EBS(OS+logiciels+configà
2. Des métadonnées = les instructiond de démarrage

## Les types d'AMI

- **AMI publiques** - fournies par AWS
- **AMI custom** - je configure et maintien comme je veux
- **AMI Marketplace** - vendues par des tiers(payant)

## AMI Process création
- Lancer une instance et l'aménager comme on veut
- Arreter l'instance pout l'intégrité des données
- ON demande à AWS de créer l'AMI(ca va créer EBS snapshots)
- Lancer de nouvelles instances a partie de ce AMI

<img width="488" height="132" alt="image" src="https://github.com/user-attachments/assets/7483311b-018f-4245-acc8-dff768ae32cf" />

## Pourquoi c'est utile

1. Lancer des instances déjà préconfigurées en quelques secondes
2. éviter de tout reconfigurer à chaque fois
3. Economise beacoup de temps
