# Week 1 Homework - Docker and SQL

## Question 1. Knowing docker tags
```bash
docker build --help
```
You can also find it in documentation [link](https://docs.docker.com/engine/reference/commandline/image_build/)

### Answer: --iidfile string

## Question 2. Understanding docker first run
```bash
docker run -it --entrypoint=bash python:3.9
```
run inside docker bash
```bash
pip list
```

Output:
```bash
Package    Version
---------- -------
pip        22.0.4
setuptools 58.1.0
wheel      0.38.4
```
### Answer: 3

## Prepare Postgres
```bash
docker network create pg-green-network
```

### Run Postgres
```bash
docker run -it \                           
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="green_ny_taxi" \
  -v /Users/rahulmandviya/Documents/GitHub/DE-Zoomcamp-2023/week-1-home-work/ny_taxi_postgres_data:/var/lib/postgresql/data \
  -p 5433:5432 \
  --network=pg-green-network \
  --name pg-database \
  postgres:13
```

### Run pgAdmin
```bash
docker run -it \
 -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
 -e PGADMIN_DEFAULT_PASSWORD="root" \
 -p 8081:80 \
 --network=pg-green-network \
 --name pgadmin \
 dpage/pgadmin4
```

### Python Ingestion Script
```bash
URL=https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-01.csv.gz

docker run -it \                           
  --network=pg-green-network \
  green_taxi_ingest:v001 \
    --user=root \
    --password=root \
    --host=pgdatabase \
    --port=5432 \
    --db=green_ny_taxi \
    --table_name=green_taxi_trips \
    --url=${URL}
```
### Build Docker Ingestion Script
```bash
docker build -t green_taxi_ingest:v001 .
```

### Run Docker Ingestion Script
```bash
docker run -it \
  --network=pg-green-network \
  green_taxi_ingest:v001 \
    --user=root \
    --password=root \
    --host=pg-database \
    --port=5432 \
    --db=green_ny_taxi \
    --table_name=green_taxi_trips \
    --url=${URL}
```

### Get Zones Data
```bash
wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv
```
> Note: Use Jupyter Notebook [upload-data.ipynb]() for ingesting `taxi+_zone_lookup.csv` Data


