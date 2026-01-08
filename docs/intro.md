# Guide Développeur Backend

Découvrez le backend Django en moins de 5 minutes.

## Démarrage Rapide

### Ce Dont Vous Aurez Besoin

* **Python 3.10+** - Vérifiez: `python --version`
* **PostgreSQL 13+** - Base de données principale
* **Redis** - Cache et tâches asynchrones
* **Git** - Gestion de version

---

## Installation

### Cloner et Installer

```bash
git clone <repository-url>
cd scenius-server
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### Configurer la Base de Données

Créez un fichier `.env`:

```bash
DEBUG=True
SECRET_KEY=votre-secret-key
DATABASE_URL=postgresql://user:password@localhost:5432/scenius_db
JWT_SECRET_KEY=votre-jwt-key
REDIS_URL=redis://localhost:6379/0
```

Appliquez les migrations:

```bash
python manage.py migrate
python manage.py createsuperuser
```

---

## Lancer le Serveur

```bash
python manage.py runserver
```

Le serveur démarre sur http://localhost:8000/

L'admin Django est accessible sur http://localhost:8000/admin/

---



## Prochaines Étapes



---

