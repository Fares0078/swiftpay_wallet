# swiftpay_wallet
 README — Projet SWIFPAY Wallet (Django /
NEXTJS)
 Introduction
Ce document présente l’ensemble du travail réalisé de A à Z dans le cadre du projet SWIFPAY WALLET,
développé pour la Nuit de l’Informatique (NIRD). Le but était de construire :
Un système de wallet (solde, historique, transactions)
Un système VIP
Une API simple mais complète (Django REST Framework)
Une faille volontaire pour démontrer un risque réel : la Mass Assignment Vulnerability
Une intégration prévue avec un front Next.js
Ce README vous explique : - Comment fonctionne chaque partie - Comment la thématique NIRD est
intégrée - Comment fonctionne la faille (et comment l’éviter) - Comment installer, lancer et tester le projet
 1. Architecture du projet
Le projet est composé de trois blocs principaux :
1. Authentification (Django + JWT)
Système d’inscription et connexion
Assignation automatique du rôle user
Rôle admin uniquement modifiable par un administrateur
2. Wallet
Un wallet est créé automatiquement pour chaque nouvel utilisateur
Champs : balance , vip , created_at , updated_at
Opérations : dépôt, retrait, consultation
3. Transactions
Chaque opération sur le wallet crée une transaction
Champs : amount , type , status , requested_at , user
Sérialiseur volontairement vulnérable (faille NIRD)
4. Thème NIRD : Cybersécurité offensive & défense
Le projet contient volontairement une faille réaliste pour : - Illustrer une attaque simple mais dangereuse -
Montrer comment l’exploiter avec Postman - Montrer les bonnes pratiques pour s’en protéger
•
•
•
•
•
•
•
•
•
•
•
•
•
•
1
 2. Thème NIRD : Comment il a été intégré
La Nuit de l’Informatique tourne autour de la cybersécurité, l’offensive et la défense. Nos objectifs :
1. Montrer une API vulnérable
Nous avons volontairement implémenté une API où l’utilisateur, via Postman, peut modifier des champs
qu’il ne devrait jamais contrôler, comme : - vip - balance - role - user_id
Le but : - comprendre comment une faille simple peut amener à une compromission totale - sensibiliser le
public aux risques backend
2. Montrer comment corriger la faille
Dans le README, nous incluons la solution complète pour bloquer la vulnérabilité.
3. Démonstration LIVE
L’équipe peut facilement : - exploiter la faille (en modifiant le wallet à 1 000 000 TND) - ensuite la corriger en
direct
 3. La FAILLE : Mass Assignment (Faille
volontaire)
 Qu’est‑ce que la "Mass Assignment Vulnerability" ?
C’est une faille très courante dans les backends non protégés.
Elle arrive quand : - le serveur accepte tous les champs envoyés par le client, même ceux qui ne devraient
pas être modifiables
Exemple d’attaque :
{
"amount": 1,
"type": "deposit",
"vip": true,
"balance": 1000000,
"role": "admin",
2
"user": 1
}
Si le backend ne filtre pas les champs, l’attaquant peut : - devenir VIP - devenir admin - changer son solde -
faire des transactions pour d’autres utilisateurs
 Pourquoi c’est grave ?
Cela permet : - d’obtenir des privilèges sans autorisation - de voler de l’argent - de consulter/modifier des
données d’autres utilisateurs - de casser totalement la logique métier
 Comment nous avons implémenté la faille ?
Dans le sérialiseur Django : - nous avons utilisé fields = '__all__' - nous avons permis l’écriture sur
tous les champs sensibles
Ceci rend l’API vulnérable exprès.
 Comment éviter cette faille (bonne pratique)
Voici les solutions correctes :
 1. Ne jamais utiliser __all__ dans un serializer
Toujours écrire explicitement les champs autorisés :
fields = ['amount', 'type']
 2. Utiliser read_only_fields
read_only_fields = ['vip', 'balance', 'user', 'status']
 3. Forcer user = request.user dans la vue
Jamais prendre user depuis le body JSON :
serializer.save(user=request.user)
 Après la démo NIRD
Vous pouvez facilement supprimer la faille en modifiant 2 lignes.
3
 4. Installation & Lancement du projet (Guide
Complet)
1. Installer les dépendances Python
Dans le dossier du projet :
pip install -r requirements.txt
2. Appliquer les migrations
python manage.py makemigrations
python manage.py migrate
3. Créer un superuser
python manage.py createsuperuser
4. Lancer le serveur
python manage.py runserver
Le backend tourne sur :
http://localhost:8000/
 5. Guide de Test (Postman)
1. ➤ Inscription
POST /api/auth/register/
4
{
"username": "fares",
"email": "fares@test.com",
"password": "12345678"
}
2. ➤ Connexion
POST /api/auth/login/ Vous recevez un access token.
3. ➤ Voir le wallet
GET /api/wallet/
4. ➤ Créer une transaction normale
POST /api/transactions/
{
"amount": 50,
"type": "deposit"
}
5. ➤ Exploiter la FAILLE
POST /api/transactions/
{
"amount": 999999,
"type": "deposit",
"vip": true,
"balance": 1000000,
"user": 1
}
Si les champs sont acceptés, la faille est confirmée.
5
 6. Conclusion
Vous avez maintenant :
Un backend Django structuré
Un système complet Wallet / Transactions / VIP
Une vulnérabilité réelle intégrée pour la démo NIRD
Un guide complet pour installer, lancer et tester
Ce README peut être envoyé directement au jury ou intégré au dépôt Git.
Besoin d’ajouter une partie sur le front Next.js ? Envie d’ajouter des images / schémas ? Je peux les ajouter
si tu veux.
•
•
•
•
6
