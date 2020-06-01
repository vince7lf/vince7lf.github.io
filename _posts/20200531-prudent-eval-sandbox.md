Salut Jonathan, j'espère que cela va bien. 

Voici mon évaluation pour les tests de la sandbox. Je recommande de la faire ré-évaluer par Marc mais surtout par la personne qui va exécuter le travail (stagiaire de ce que je comprends). C'est généralement ce que je fais de mon côté lorsque je reçois un mandat déjà évalué par quelqu'un d'autre. + recevoir plusieurs évaluations de différentes ressources permet de mieux cerner "le scope" et détecter les points d'incohérences entre les diverses parties. Et donc partir le projet avec une meilleure compréhension et de meilleures attentes.

Côté effort, je peux fournir entre 10-15 heures de mon temps par mois jusqu'à septembre 2020, ensuite probablement un peu plus, selon le nombre de cours que je prends (1 ou 2 cours de 3 crédits chaque -- mes 2 derniers cours !!!). Il faut vraiment que je concentre mon énergie pour compléter mon essai de recherche avant la rentrée. 

# Évaluation sandbox

J'indique une évaluation min-max (Optimiste-Pessimiste). 
* 5-10 jours
* avec option d'optimisation: +1-4 jours
## Hypothèses
* Serveur de BD est postgreSQL/PostGIS ;
* Serveur carto est MapServer;
* Serveur Web est Apache;
* Applicatif est Web avec librairies javascript carto OpenLayers ou Leaflet;
* Logiciels SIG disponibles: 
 * ArcGIS Pro
 * QGIS
## Détails  de l'évaluation
### Validation de l'environnement - 0.5-1 jour
* Valider les connexions aux différents éléments (serveur BD, serveur carto, serveur web); 
** connexion internes et depuis l'Internet (serveur web et serveur BD); 
* Faire des tests de bases comme recommandé par la documentation de chaque élément ; 
### Données - 1-2 jours
* Mise en place du schéma de données (conception / design); 
* Importation des données d'un petit projet pilote ArgGIS Pro (petite gdb, quelques shape files de clients, quelques points d'intérêt); script ou à la main; 
* Mise en place de l'accès à la BD depuis l'externe (Internet); 
### Serveur Carto - 0.5-1 jour
* Configurer le serveur carto pour qu'il puisse accéder aux données géospatiales de la BD; 

### Serveur Web - 0.5-1 jour
* Configurer le serveur web afin que des applications web en Javascrip puissent être gérées; 
* Configurer le serveur web afin que les requêtes puissent être redirigées au serveur carto; 

### Applicatif - 2-4 jours
* Évaluer les applications disponibles du côté Esri pour accéder aux données directement (sans passer par le serveur web/carto) et/ou en passant par le serveur web/carto; comparer (performances, délais) ; 
* Concevoir une petite application Web cartographique (avec librairies Javascript carto) bien simple, qui se connecte au serveur web; comparer avec l'applicatif Esri (performances, délais) ; 
### Dynamisme - 1-2 jours
* Faire des tests de rafraichissement de l'application Web et application Esri lorsque des données sont mises à jour dans la base de données ;
* Développement d'un script de test qui modifie les données (ajout - retrait - modification) selon des scénarios prédéfinis (hardcoded); 
* Adapter l'application web pour qu'elle recharge le contenu en tenant compte de l'expérience client (pas de ralentissement, de clignotement, de perte de contexte, etc);  
### Optimisation (optionnel) - 1-4 jours
* Serveur de base de données : voir les paramètres d'optimisation possible (nombre de connexions concurrentes, mémoire, indexation, gestion du cache, multithreading/CPU, etc); se référer à la documentation et faire des tests; 
* Serveur carto: voir les paramètres d'optimisation possible; se référer à la documentation et faire des tests; 
* Serveur Web: voir les paramètres d'optimisation possible; se référer à la documentation et faire des tests; 
* Application: voir les paramètres d'optimisation possible; se référer à la documentation et faire des tests; 
### Banque de temps supplémentaire pour les délais inattendus et les imprévus (non inclus dans le total)
* +/- 1-3 jours
# Phases futures (non incluses)
* Optimisation (client app; serveurs ) ;
* Restrictions (role; accès aux features selon le login) ;
* Sécurité (connexions HTTPS/TLS) ; 
* Audit (client; admin prudent; scripts) ;
* Dynamisme; mise à jour temps réel ;
* Collaboration; l'ajout de feature est partagé en temps réel; 
* Préférences utilisateurs (exe: conserver la dernière vue) ;
* Continuité du service (haute disponibilité; load balance) ;
