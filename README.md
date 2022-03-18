# CDC with Meltano

1. Create virtual environment
```shell
pyenv virtualenv 3.9.10 meltano-sandbox39
pyenv activate meltano-sandbox39
```
2. Install Meltano
```shell
pip install meltano
```
3. Create a new meltano project
```shell
meltano init meltano-cdc
cd meltano-cdc
```
4. Install the Postgres Singer tap
```shell
meltano add extractor tap-postgres
```
5. Configure tap
```shell
meltano-cdc/meltano.yml
```
6. Export secrets
```shell
export TAP_POSTGRES_PASSWORD=my_password
```
7. Start Postgres
```shell
nerdctl compose up
```
8. Create Pagila database
```shell
nerdctl exec -it meltano-sandbox_db_1 psql -U postgres
postgres=# CREATE DATABASE pagila;
```
9. Create Pagila schema and insert all data
```shell
cat pagila/pagila-schema.sql | nerdctl exec -i meltano-sandbox_db_1 psql -U postgres -d pagila
cat pagila/pagila-data.sql | nerdctl exec -i meltano-sandbox_db_1 psql -U postgres -d pagila
```
10. Add sink
```shell
meltano add loader target-csv
```
11. Start ELT pipeline
```shell
meltano elt tap-postgres target-csv --job_id=postgres-to-csv-cdc
```