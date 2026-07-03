# EC2 Advanced

## Placement Groups

Une consigne donnée à AWS sur COMMENT placer phydiquement tes instances les unes par rapport aux autres

### Les 3 Stratégies

**Cluster** = instances regroupées, 1 seule AZ, matériel rapproché

<img width="390" height="186" alt="image" src="https://github.com/user-attachments/assets/a5755e40-9db4-4629-8eb6-4752e8461acf" />

- But : réseau ultra rapide, latence minimale
- Risque : si AZ tombe, tout tombe
- Usage : HPC, big data

**Spread** = chaque instance sur un matériel physique distinct, plusieurs AZ(max 7 instancees par groupe par AZ)

<img width="523" height="366" alt="image" src="https://github.com/user-attachments/assets/a36775b2-ad03-4fcb-baf8-069f2b4a5b2d" />

- But : résilence maximale
- Limite : max 7 instances par AZ
- Usage : les application qui ont besoin de maximiser la disponibilité

**Partition** = instances réparties en groupes(partitions), matériel séparé par partitions

<img width="450" height="358" alt="image" src="https://github.com/user-attachments/assets/ffa801f9-601b-4d84-a306-090080b06921" />

- But : beacoup d'instances + isolation par groupe
- Notes :
  - Instances d'une partition ne doivent pas partager le meme rack ave une autre partition
  - Une panne n'affecte q'une partition
  - Pour connaitre la partition d'une instance, on regarde metadata
  - Usage : gros systèmes distribues (Kaka, Cassandra, HDFS)
 
### Attribuer une instance à une groupe

`Créer Instance > Advanced Settings > Placement Group`

## Elastic Network Interfaces(ENI)

C'est la carte réseau, un composant qui permet de communiquer sur le réseau.ENI est le point départ et sert a toute communication.

**ENI peut avoir les atributs suivants:**
- IPv4 primaire ou un ou plus IPv4 secondaires
- un Elastic IP par IPv4 privé
- Un IP publique
- Un ou plusieurs **security groups**
- MAC adress
<img width="322" height="278" alt="image" src="https://github.com/user-attachments/assets/752f1b88-3000-4c2f-a28f-fd649702c595" />

1. L'ENI est **indépendante** et **déplacable**.Je peux la détacher d'une instance et la rattacher a une autre(fly)
2. Si une instance tombe, je déplace son ENI(avec son IP et ses security groups) vers une instance de secours(failover)

## Hibernate

- Stop(arret normal) : la RAM est perdue. Au redémarrage, l'OS reboote de zéro
- Terminate : l'instance est supprimé définitivement
- Hibernate : préserve le contenu de la RAM

### Ce que fait Hibernate

- Au lieu de tout éteindre, l'état de la RAM est sauvegardé sur le disque EBS root
- Au redémarrage, la RAM est rechargé telle quelle
- Reprise très rapide(pas de long boot, pas de rechargement d'appli)
<img width="257" height="377" alt="image" src="https://github.com/user-attachments/assets/98d3fd94-302c-4a82-bdf7-230fb6bc779a" />

### Cas d'usage

- Services longs à démarrer
- Garder l'état de RAM

### Conditions / limites

- La RAM est sauvegarddé sur l'EBS root, il doit etre chiffré et asses grand pour contenir toute la RAM
- Instance RAM taille doit etre < 150GB
- Ne pas hiberner plus de 60 jours d'affilée
- N'est pas supporté pour **métal nu**(une instance ou on a accés directement au serveur physique, sans la couche de virtualisation)

**Virtualisation** - découper un serveur physique en plusieurs machines virtuelles isolées
**Hyperviseur** - le logiciel qui fait ce découpage

**Sans hyperviseur le hibernate n'est pas possible car serveur physique, sans virtualisation ne peut pas prendre l'image de RAM***

### Hibernate — pratique
- À la création de l'instance → Advanced details → activer "Stop - Hibernate behavior".
- Ensuite, pour mettre en pause : Actions → Stop → Hibernate.
- Au redémarrage, l'état de la RAM (donc l'OS et les process) est conservé tel quel : pas de reboot complet, on reprend où on s'était arrêté.


