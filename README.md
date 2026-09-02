# harice-mariam-test-1-1

Service Harice (`harice-mariam-test-1-1`), genere automatiquement par le Maestro.

- Type : nest
- Port : 2008
- Image : systeodigital/harice-mariam-test-1-1

Le CI/CD est automatique : a chaque push, Jenkins build l'image,
la pousse, puis la deploie via `docker compose` sur le serveur.
Le `Jenkinsfile` et le `docker-compose.yml` sont deja prets.

## En local
```bash
docker build -t systeodigital/harice-mariam-test-1-1:dev .
docker run --rm -p 2008:2008 systeodigital/harice-mariam-test-1-1:dev
```
