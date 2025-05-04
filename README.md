Step 1: Download the docker-compose.yaml file 
```
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.9.0/docker-compose.yaml'
```

> if you are interested in other versions of docker, change the 2.9.0 to which ever version you want

Step 2: Create necessary files and set airflow uid
```bash
mkdir -p ./dags ./logs ./plugins ./config
echo -e "AIRFLOW_UID=$(id -u)" > .env
```

Step 3: Create a [Dockerfile](./Dockerfile) and define the logic you want to use to build your custom image. In my case
+ I used apache/airflow:2.9.0 as my base image
+ copied my local requirements.txt file to the root folder in the container
+ upgraded pip
+ Installed/updated some packages

Step 4: Build the custom image from the Dockerfile
This is the syntax
```bash
docker build -t name:version .
```

In my case, the command is

```bash
docker build -t airflow-extended:1.0.0 . 
```
The name of my custom image is `airflow-extended:1.0.0`

> Note, you have to be in the same directory as the Dockerfile

Step 5: Modify the docker-compose.yaml file by changing the default image line
```yaml
image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:2.9.0}
```
to the name of your custom image
```yaml
image: ${AIRFLOW_IMAGE_NAME:-airflow-extended:1.0.0}
```

Step 6: Initialize your database and make sure it exits with code 0

```bash
docker compose up airflow-init
```

Step 7: Now you can start all services
```bash
docker compose up
```