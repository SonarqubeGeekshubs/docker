# Run SonarQube with a PostgreSQL database

## Requirements

 * Docker CE 1.18.x

## Compose file

Execute this `docker-compose.yml` file:

```yaml
version: "3"

services:
  sonarqube:
    image: sonarqube
    ports:
      - "9000:9000"
    environment:
      - SONARQUBE_JDBC_URL=jdbc:postgresql://db:5432/sonar
    volumes:
      - sonarqube_conf:/opt/sonarqube/conf
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_bundled-plugins:/opt/sonarqube/lib/bundled-plugins

  db:
    image: postgres:9.6
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
    volumes:
      - postgresql:/var/lib/postgresql
      # This needs explicit mapping due to https://github.com/docker-library/postgres/blob/4e48e3228a30763913ece952c611e5e9b95c8759/Dockerfile.template#L52
      - postgresql_data:/var/lib/postgresql/data

volumes:
  sonarqube_conf:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_bundled-plugins:
  postgresql:
  postgresql_data:
```

Use [docker-compose](https://github.com/docker/compose) to start the containers.

```bash
$ docker-compose up
```

Restart the containers (after plugin upgrade or install for example).

```bash
$ docker-compose restart sonarqube
```

Analyse a project:

```bash
mvn sonar:sonar \
  -Dsonar.host.url=http://$(boot2docker ip):9000 \
  -Dsonar.jdbc.url=jdbc:postgresql://$(boot2docker ip)/sonar
```

## To be improved

 + Backup
 + Clustering
 + Upgrade
 + Admin password
 + Plugins
 + ...
