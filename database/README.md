# Si en algún momento quereis empezar de cero:
# docker compose down -v

docker compose up --build

# Crea la BBDD
docker exec -it sql-server /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Password_2023 -i "/scripts/database.sql"

# Crea las tablas
docker exec -it sql-server /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Password_2023 -i "/scripts/tablas.sql"

# Tirar queries:
docker exec -it sql-server /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Password_2023