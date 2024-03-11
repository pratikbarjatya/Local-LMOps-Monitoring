# Local MLOps Monitoring

## Prerequisites

You need following tools installed:
- `docker`
- `docker-compose` (included to Docker Desktop for Mac and Docker Desktop for Windows )

## Tools we are using

- Docker
- Docker compose
- Prefect
- Grafana
- Postgres
- Evidently

## Preparation

Note: all actions expected to be executed in repo folder.

- Create virtual environment and activate it (eg. `python -m venv venv && source ./venv/bin/activate` or `conda create -n venv python=3.11 && conda activate venv`)
- Install required packages `pip install -r requirements.txt`

- I am using this for my environment 
```
`conda create -n py11 python=3.11 && conda activate py11`
```
- Run `baseline_model_nyc_taxi_data.ipynb` for downloading datasets, training model and creating reference dataset 
```
jupyter notebook
```
And open  `http://localhost:8888/tree?token=beb2fbe1b95bf92927c0a58e591e132b74a782932a322fe6` like this which you will find in your terminal and run all the blocks in `baseline_model_nyc_taxi_data.ipynb` file.


### Starting services

To start all required services, go to new terminal and execute:
```bash
docker-compose up
```

It will start following services:
- `db` - PostgreSQL, for storing metrics data
- `adminer` - database management tool
- `grafana` - Visual dashboarding tool 

### Running Prefect

Open a new terminal, activate the virtual environemnt by running this `conda activate py11` and then run this command to start the prefect server.
```
prefect server start
```

And run this in new terminal
```
prefect config set PREFECT_API_URL=http://127.0.0.1:4200/api
```

Check out the dashboard at `http://127.0.0.1:4200`


### Sending data

To calculate evidently metrics with prefect and send them to database, execute:
- open new terminal and run the conda environment :
```
conda activate py11
```
Then run this command :
```bash
python evidently_metrics_calculation.py
```

This script will simulate batch monitoring. Every 10 seconds it will collect data for a daily batch, calculate metrics and insert them into database. This metrics will be available in Grafana in preconfigured dashboard. 

### Accsess dashboard

- In your browser go to a `localhost:3000`
The default username and password are `admin`

- Then navigate to `General/Home` menu and click on `Home`.

- In the folder `General` you will see `New Dashboard`. Click on it to access preconfigured dashboard.

### Ad-hoc debugging

Run `debugging_nyc_taxi_data.ipynb` to see how you can perform a debugging with help of Evidently `TestSuites` and `Reports`

### Stopping services

To stop all services, execute:
```bash
docker-compose down
```
