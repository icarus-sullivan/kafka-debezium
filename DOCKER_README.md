# Pre-Up

1. Create a password for our UI's
    ```
    htpasswd -c nginx/.htpasswd admin
    ```

2. Modify your local /etc/hosts for easier subdomain access of UI's

  ```
  echo "127.0.0.1 kafka.localhost debezium.localhost" | sudo tee -a /etc/hosts > /dev/null
  ```


# Run Up

Run the following command:

```
docker-compose up -d
```

1. Wait for services to initialize (check logs if needed).

2. Register a Debezium Postgres source connector (example users replication):

  ```
  curl -X POST http://localhost:8083/connectors \
-H "Content-Type: application/json" \
-d '{
  "name": "postgres",
  "config": {
    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
    "plugin.name": "pgoutput",
    "database.hostname": "postgres",
    "database.port": "5432",
    "database.user": "myuser",
    "database.password": "mypassword",
    "database.dbname": "mydb",
    "database.server.name": "localhost",
    "topic.prefix": "debezium",
    "table.include.list": "public.users",
    "database.include.schema.changes": false,
    "snapshot.mode": "initial",
    "schema.history.internal.kafka.bootstrap.servers": "kafka:9092",
    "schema.history.internal.kafka.topic": "schema-changes.inventory",
    "key.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "key.converter.schemas.enable": "false",
    "value.converter.schemas.enable": "false"
  }
}'
```

> 🔍 Notes:
> - pgoutput is the plugin used with logical replication.
> - Debezium automatically creates the replication slot and publication if configured.
> - You can view Kafka topics at http://kakfa.localhost (Kafka UI).
> - You can view Debezium connectors at http://debezium.localhost (Debezium UI).
