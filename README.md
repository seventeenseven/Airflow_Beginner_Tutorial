# Guide débutant : Airflow - Création de DAGs et automatisation de tâches

Bienvenue dans ce tutoriel sur **Apache Airflow**, un outil puissant pour l’orchestration et l’automatisation des workflows. Ce guide vous montrera comment créer et déployer vos premiers DAGs (Directed Acyclic Graphs) en utilisant des exemples simples.

## Qu'est-ce qu'Apache Airflow ?

Apache Airflow est une plateforme open-source permettant de planifier, superviser et surveiller des workflows. Il est basé sur Python, ce qui le rend très flexible pour les développeurs et les data engineers.

**Principales caractéristiques :**
- Gestion de workflows complexes sous forme de DAGs.
- Intégration facile avec des outils tiers (bases de données, API, etc.).
- Interface utilisateur riche pour surveiller les workflows.

## Installation d'Airflow

### Prérequis
- Python 3.8 ou plus.
- Un environnement virtualenv ou conda est recommandé pour isoler les dépendances.

### Installation
1. Créez un environnement virtuel :
   ```bash
   python -m venv airflow_env
   source airflow_env/bin/activate
   ```
2. Installez Apache Airflow  :
   ```bash
   pip install "apache-airflow==2.10.3" --constraint "https://raw.githubusercontent.com/apache/airflow/constraints-2.10.3/constraints-3.8.txt"
   ```
3. Initialisez la base de données Airflow :
   ```bash
   airflow db init
   ```
4. Créez un utilisateur administrateur :
   ```bash
   airflow users create --username admin --firstname Alexandra --lastname Jane --role Admin --email jane@airflow.org.
   ```
5. Lancez Airflow :
   ```bash
   airflow webserver --port 8080 &
   airflow scheduler &
   ```
6. Accédez à l’interface Web sur [http://localhost:8080](http://localhost:8080). Et connectez-vous avec les informations d'admin créees précedemment.

## Premiers pas avec les DAGs

Un DAG est une représentation de votre workflow en tâches interconnectées. Voici quelques exemples pratiques pour comprendre comment créer des DAGs.

### Exemple 1 : Hello World DAG

Ce DAG illustre comment créer une tâche Python simple qui imprime "Hello World" et d’autres tâches connectées.

```python
from datetime import datetime
from airflow import DAG
from airflow.operators.empty import EmptyOperator
from airflow.operators.python import PythonOperator

# Fonction Python exécutée par une tâche
def print_hello():
    return 'Hello world from Airflow DAG!'

# Création du DAG
dag = DAG('hello_world', description='Hello World DAG',
          schedule_interval='0 12 * * *',  # Planification : tous les jours à midi
          start_date=datetime(2022, 10, 10), catchup=False)

# Définition des tâches
hello_operator = PythonOperator(task_id='hello_task', python_callable=print_hello, dag=dag)
great_op = EmptyOperator(task_id='great_task')
bye_op = EmptyOperator(task_id='goodbye')

wu_op = EmptyOperator(task_id='whatsup_task')
wish_op = EmptyOperator(task_id='wish_task')
oops_op = EmptyOperator(task_id='oops_task')

# Dépendances entre les tâches
hello_operator >> [great_op, wu_op] >> bye_op 
bye_op << [wish_op, oops_op]
```

### Exemple 2 : Envoi d’e-mail

Ce DAG montre comment configurer une tâche pour envoyer un e-mail.

```python
from airflow import DAG
from airflow.operators.email import EmailOperator
from airflow.operators.empty import EmptyOperator
from datetime import datetime

# Création du DAG
with DAG("email_example", description="Envoi d'un e-mail",
         start_date=datetime(2022, 10, 10), schedule_interval=None) as dag:

    # Tâche d'envoi d'e-mail
    email = EmailOperator(
        task_id="send_email",
        to="admin@example.com",
        subject="Update complete",
        html_content="<p>Bonjour, la mise à jour est terminée !</p>",
    )

    # Tâche vide (placeholder)
    nothing = EmptyOperator(task_id="nothing")

    # Dépendance : e-mail avant tâche vide
    email >> nothing
```

## Contributions
Si vous avez des idées pour améliorer ce tutoriel ou ajouter des exemples, n’hésitez pas à soumettre une pull request !
