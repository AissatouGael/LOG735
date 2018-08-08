# ÉTS : LOG735-01 Systèmes distribués (Été 2018)

## Pré-requis

### Installer Docker CE :

Suivre les instructions du site officiel, spécifiques à votre système d'exploitation : https://docs.docker.com/install/

## Exécution des tests et collecte des métriques pour MongoDB.

1.  Télécharger les images et démarrer les conteneurs.
```console
docker-compose -f .\docker-compose.yml up
```

2.  (Étape optionnelle) Tester si tout fonctionne bien.
```console
http://localhost:8081
```

3.  Lister les conteneurs pour récupérer l'ID du conteneur mongo.
```console
docker ps
```

4.  SSH dans le conteneur.
```console
docker exec -it <ID du conteneur mongo> bash
```

5.  Se connecter au shell mongoDB.
```console
mongo admin -u log735 -p password123
```

6.  Créer une base de données.
```console
use SystemDistribue
```

7.  Créer les données de tests.
```console
for(i=1; i<=100000; i++){
	var dixmill = i%10000;
	var mill = i%1000;
	var cent = i%100;
	db.produits.insert({compteur:i, dixmill:dixmill, mill:mill, cent:cent })
}
```

8.  Récupérer les métriques.
```console
db.serverStatus() >> outputMetricsMongoDb.txt
```

## Exécution des tests et collecte des métriques pour Cassandra.

1.  Démarrer un cluster.
```console
docker run --name some-cassandra -d cassandra:latest

docker run --name some-cassandra2 -d -e CASSANDRA_SEEDS="$(docker inspect --format='{{ .NetworkSettings.IPAddress }}' some-cassandra)" cassandra:latest
```

2.  Création d'un keyspace et d'une table.
```console
docker run -it --link some-cassandra:cassandra --rm cassandra sh -c 'exec cqlsh "$CASSANDRA_PORT_9042_TCP_ADDR"'

cqlsh> CREATE KEYSPACE IF NOT EXISTS log735 WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 3 };

CREATE TABLE log735.SystemDistribue ( id UUID PRIMARY KEY, compteur int, dixmill int, mill int, cent int );
```

3.  Créer les données de tests.
```console
touch ~/output.cql

echo "BEGIN BATCH" >> ~/output.cql
for i in $(seq 1 100000)
do
    UUID=$(cat /proc/sys/kernel/random/uuid)
    compteur=$i
	dixmill=$(($i%10000))
	mill=$(($i%1000))
	cent=$(($i%100))
    echo "INSERT INTO log735.SystemDistribue (id, compteur, dixmill, mill, cent) VALUES ($UUID, $compteur, $dixmill, $mill, $cent);" >> ~/output.cql
done
echo "APPLY BATCH;" >> ~/output.cql

cqlsh "$CASSANDRA_PORT_9042_TCP_ADDR"

SOURCE '~/output.cql'
```

8.  Récupérer les métriques (Erreur de connexion).
```console
nodetool tablestats log735.SystemDistribue >> outputMetricsCassandra.txt
```

## [P]roblème rencontrés, et [S]olutions trouvés

1.  P : Hyper-V est activé, mais non reconnu par Docker.
    S : Dans la section « Turn Windows features on or off », Désactiver Hyper-V, redémarrer, Réactiver Hyper-V et redémarrer encore. Docker devrait fonctionner.

2.  P : Durant l'installation, une erreur concernant `virt` peut survenir.
    S : Il faut exécuter la commande suivante pour y remédier :
        ```console
        sudo apt-wget install virtualbox
        ```
