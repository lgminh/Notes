# Setting up Apache Airflow 1.8.1 on Ubuntu 16.04

**Step 1**	
 - Create [virtual environment in Python](http://docs.python-guide.org/en/latest/dev/virtualenvs/)  
 - Active your virtualenv by running `source <your_virual_env_name>/bin/active`.
			
**Step 2**
- Install apache-airflow by running `pip install apache-airflow[extra-package]` if you use Python 2.X, otherwise (Python 3.X) then run `pip3 install apache-airflow[extra-package]` . You can see what **extra-package** is for your need [here](https://airflow.apache.org/installation.html). 		
- Just run `pip install apache-airflow` if you want to install all package.

**Step 3:**
- Run docker postgres by running `docker run --name postgres -e POSTGRES_USER=<your_user> -e POSTGRES_PASSWORD=<your_password> -e POSTGRES_DB=<your_db> -p 5432:5432 -d mdillon/postgis:9.5`
	
**Step 4**
- Create folder `airflow` to contain airflow config, dags, etc.

**Step 5:**
- `cd <your_folder_name>`

**Step 6**
- Set $AIRFLOW_HOME= /root/../airflow by running `export AIRFLOW_HOME= ~/airflow`.

**Step 7**
- Run `airflow initdb` and airflow will create `airflow.cfg`

**Step 8**
- Create folder `dags` to contain all your dags inside folder `airflow`.
- Open file `airflow.cfg`.
- Find the line that starts with `sql_alchemy_conn = postgresql+psycopg2//...` and change it into `sql_alchemy_conn = postgresql+psycopg2://<docker_postgres_user>:<docker_postgres_password>@localhost:5432/<your_db>`
- Find the line that starts with `executor = CeleryExecutor` and change in into  `executor = LocalExecutor`.

**Step 9**
- Start airflow webserver/scheduler by running `airflow webserver -p <your_port>` and `airflow scheduler`
- If you want to **run webserver/schduler in background**, install **nohup** and run 
`nohup airflow webserver -p <your_port> -D &
nohup airflow scheduler &`
	
**Step 10 (Optional) : Setting up Airlow behind Nginx as Proxy Server**
- Install Airflow
- Install Nginx
- Allow **Nginx HTTP** and **Nginx HTTPS** using Ubuntu firewall.
- Create `/etc/nginx/con.d/airflow.conf`.
- Add this [server block](https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms) into `airflow.conf`.
	```
	upstream airflow {
	         server 127.0.0.1:<airflow_port> fail_timeout=0;
	}
	ssl_certificate <your_crt_file_path>;
	ssl_certificate_key <your_key_file_path>;
	ssl_stapling on; # Requires nginx >= 1.3.7
	ssl_stapling_verify on; # Requires nginx => 1.3.7
	resolver_timeout 5s;

	location / {
			 proxy_pass http://airflow;
	}```

- Reload nginx: `systemctl reload nginx`.

**Adding new dags**
- Prepare your python script.
- Copy your python script into folder `dags`.
- Run `python -m py_compile <your_python_script>.py`.
- Move `<your_python_script>.pyc` out of `__pycache__` inside `dags`.
- Restart `webserver` and `scheduler`. (If you get error `already running on PID` then remove `airflow-webserver.pid` and `airflow-scheduler.pid` first)
- Open Web UI to check your DAG.
