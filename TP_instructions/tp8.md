summary: TP8
id: tp8
categories: tp, api
tags: api, flask
status: Published
authors: OCTO Technology
Feedback Link: https://github.com/octo-technology/Formation-MLOps-2/issues/new/choose

# TP8 - Monitoring

## Vue d'ensemble

Durée : 60 min

### À l'issue de cette section, vous aurez découvert :

- Comment implémenter une sonde de monitoring de vos prédictions
- Comment créer et alimenter une DB avec les valeurs monitorées
- Comment créer un dashboard de monitoring dans Grafana

### Présentation des nouveautés sur la branche de ce TP

Pour ce TP, utilisez la branch 8_starting_monitoring :

`git checkout 8_starting_monitoring`

Sur cette branche, il y a maintenant :

- Un fichier `formation_indus_ds_avancee/monitoring.py` qui contient 2 fonctions `monitor_with_io` et `monitor`
- Un fichier de tests correspondant : `tests/test_unit/test_monitoring.py` qui teste la fonction `monitor_with_io`
- Une tâche en plus dans le DAG `dags/predict.py` qui exécute le monitoring.

## Monitoring des prédictions et enregistrement dans une DB

### Définir une sonde de monitoring sous forme de fonction Python

Dans le fichier `formation_indus_ds_avancee/monitoring.py`, créez une fonction `monitor` qui retournera la valeur de
votre choix à monitorer. Cette valeur sera enregistrée dans la DB PostgreSQL grâce à la fonction `monitor_with_io`
associée à une tâche Airflow.

Vous devez écrire une ligne par run, donc créer un dataframe qui contient 1 ligne et au moins 2 colonnes
le `prediction_time` et un indicateur agrégé,
par exemple la moyenne de prédictions calculée à ce moment-là

### Tester

Mettre à jour le test unitaire dans `tests/test_unit/test_monitoring.py` pour qu’il soit vert.

### Choisir la table dans la base de données

Spécifier la table PostgreSQL dans laquelle enregistrer les valeurs à monitorer

Dans le fichier `dags/config.py`, définir la constante `MONITORING_TABLE_NAME` avec un nom unique
différent de celui des autres participants. Cette table sera désormais alimentée par la tâche `monitor` d'Airflow.

Une fois cela fait, retirer du fichier `.github/worflows/ci.yml` la
ligne `continue-on-error: true # Task is expected to fail (there is a test that trainee will fix"`
qui permettait d'éviter à la CI de fail, même si vous n'avez pas fait cette modification.

### Re-démarrer Airflow

Relancer Airflow et le DAG de prédiction

La tâche `monitor` devrait s'exécuter. Il nous faut désormais créer un dashboard de suivi des valeurs renvoyées par
notre fonction.

## Affichage d'un dashboard de monitoring dans Grafana

Nous souhaitons maintenant afficher nos valeurs monitorées dans Grafana à partir de la table PostgreSQL.

- Accéder à Grafana à l'adresse `https://lab.aws.octo.training/grafana` et se connecter avec l'identifiant `admin` et le
  mot de passe `admin`

- Créer la connexion à la DB PostgreSQL

Nous allons ajouter une *Data Source* de type *PostgreSQL* et spécifier les paramètres suivants :

![sdata-source-grafana](./docs/tp8/data-source-grafana.png)

- Host : postgres:5432
- Username: postges
- Password: postgres

- Créer un simple dashboard de monitoring.

Il vous suffit de cliquer sur *New dashboard* > *Add Query*, et de spécifier la connexion et la table PostgreSQL
(personnelle) créées lors des étapes précédentes.