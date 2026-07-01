# IAM (Identity and Access Management)

## Bases
1. IAM est **global** — pas de région.
2. **Root account** : ne jamais l'utiliser au quotidien. Activer le MFA dessus.
   Créer un user et lui attacher une **policy** administrateur (via un groupe).

## User / Group / Role (à ne pas confondre)
- **User** = une personne.
- **Group** = un ensemble d'users (un groupe ne peut pas contenir un autre groupe).
- **Role** = donne des permissions à un **service** AWS (ex : une EC2 qui accède à S3),
  pas à une personne.

## Policies
Les permissions se donnent via des **policies** (documents JSON).
Une policy peut être attachée à un user, un group, ou un role.

**Structure** :
```
{
  "Effect": "Allow",
  "Action": "iam:GetUser",
  "Resource": "*"
}
```
- **Effect** : Allow / Deny
- **Action** : ce qui est autorisé
- **Resource** : sur quoi

1. Deny explicite → REFUSÉ (gagne toujours)
2. sinon Allow explicite → AUTORISÉ
3. sinon (rien) → REFUSÉ par défaut (**implicit deny**)
