Airflow Tutorial

Tutorial Link - https://www.udemy.com/course/the-complete-hands-on-course-to-master-apache-airflow/
Installation ond Airflow:

    python3 -m venv sandbox
    source sandbox/bin/activate
    pip3 install wheel
    pip3 --no-cache-dir install apache-airflow==2.1.0 --constraint https://gist.githubusercontent.com/marclamberti/742efaef5b2d94f44666b0aec020be7c/raw/21c88601337250b6fd93f1adceb55282fb07b7ed/constraint.txt

Start Airflow:

    airflow db init
    airflow users create --username admin --password admin --firstname Admin --lastname Admin --role Admin --email admin@airflow.com

Change default conf of sql_alchemy:
By default the sqlite is configured but for parallel processing of task we cannot have sqlite, we will need postgres

    to check default conf - airflow config get-value core sql_alchemy_conn you will get "sqlite:////home/airflow/airflow/airflow.db"
    To use parallel processing the executor shoul be "LocalExecutor", to achive the same follow the commands.
    sudo apt update
    sudo apt install postgresql
    sudo -u postgres psql
        ALTER USER postgres PASSWORD 'postgres';
    All Airflow features known to man - pip install 'apache-airflow[all]'
    airflow db init
    pip install apache-airflow['cncf.kubernetes']
    airflow users create --username admin --password admin --firstname Admin --lastname Admin --role Admin --email admin@airflow.com

Install Elastic search

    curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
        password is airflow
    echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
    sudo apt update && sudo apt install elasticsearch
    pip install elasticsearch==7.10.1
    Start Elasticsearch with - sudo systemctl start elasticsearch
    Check if Elasticsearch works with - curl -X GET 'http://localhost:9200'

Use docker-compose for running Airflow in multiple containers - airflow-local folder.
To install and run docker-compose.yaml

    Install the docker Engine from https://docs.docker.com/engine/install/ubuntu/
    Then docker desktop - https://docs.docker.com/desktop/linux/install/ubuntu/
    Read and understand the flow - https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html. Run these commands - https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html#setting-the-right-airflow-user
    Then in the docker desktop setting >> General >> Enable Docker Compose V1/V2 compatibility mode.
    Then run the command - docker-compose -f docker-compose.yaml up -d
    Note: normally you will see the docker-compose -v as 1.2.5 but after point (4) you should see Docker Compose version v2.5.0.

ERROR: 8080 port already in use

    When I run the command docker-compose -f docker-compose.yaml up -d, for webserver
        I got this error - Error response from daemon: Ports are not available: exposing port TCP 0.0.0.0:8080 -> 0.0.0.0:0: listen tcp 0.0.0.0:8080: bind: address already in use
    I check the port access for 8080 - sudo lsof -t -i:8080 it gave me 2 ports in use 2078, 2084
    The netstat cmd will give the name of the services these port are running - sudo netstat -tulpn | grep LISTEN, Services - 2078/docker-proxy, 2084/docker-proxy.
    I killed them using - sudo kill -9 2078
    Run the command docker-compose -f docker-compose.yaml up -d again.

If you want to run docker as non-root user then you need to add it to the docker group.

    Create the docker group if it does not exist: sudo groupadd docker
    Add your user to the docker group: sudo usermod -aG docker $USER
    Run the following command or Logout and login again and run (if that doesn't work you may need to reboot your machine first): $ newgrp docker
    Check if docker can be run without root: docker run hello-world
    Reboot if still got the error: reboot