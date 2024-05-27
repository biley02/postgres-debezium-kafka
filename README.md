================================================================

- docker-compose down (to remove all docker containers)
- docker-compose up --build OR docker-compose up

Note: If any other process is running at some port like 8081, then do:
- kill -9 $(lsof -ti:8081)

===============================================================


Create debezium postgres connector:

curl --location '127.0.0.1:8083/connectors' \
--header 'Accept: application/json' \
--header 'Content-Type: application/json' \
--data '{
"name": "exampledb-connector",
"config": {
"connector.class": "io.debezium.connector.postgresql.PostgresConnector",
"plugin.name": "pgoutput",
"database.hostname": "host.docker.internal",
"database.port": "5432",
"database.user": "db_user",
"database.password": "db_password",
"database.dbname": "db_name",
"database.server.name": "postgres",
"table.include.list": "public.jobs"
}
}
'


===============================================================

Kafka client to view the messages:

- docker run --tty --network postgres_debezium_cdc-master_default  confluentinc/cp-kafkacat kafkacat -b kafka:9092 -C -r http://schema-registry:8081 -s key=s -s value=avro -t postgres.public.jobs