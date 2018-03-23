# Setting up Apache Airflow 1.8.1 on Ubuntu 16.04

**Step 1**	
 - Create [virtual environment in Python](http://docs.python-guide.org/en/latest/dev/virtualenvs/)  
 - Active your virtualenv by running `source <your_virual_env_name>/bin/active`.
			
**Step 2**
- Install apache-airflow by running `pip install apache-airflow[extra-package]` if you use Python 2.X, otherwise (Python 3.X) then run `pip3 install apache-airflow[extra-package]` . You can see what **extra-package** is for your need [here](https://airflow.apache.org/installation.html). 		
- Just run `pip install apache-airflow` if you want to install all package.
	
**Step 3**
- Create folder `<your_folder_name>` to contain airflow config, dags, etc.

**Step 4**
- Set $AIRFLOW_HOME= <your_folder_path> by running `export AIRFLOW_HOME= <your_folder_path>`.

**Step 5**
- Create folder `dags` to contain all your dags.
- Create file `<your_folder_name>/airflow.cfg`.
- Find the line start with `executor = CeleryExecutor` and change in into  `executor = LocalExecutor`.

**Step 6**
- Start airflow webserver/scheduler by running `airflow webserver -p <your_port>` and `airflow scheduler`
- If you want to **run webserver/schduler in background**, install **nohup** and run 
`nohup airflow webserver -p <your_port> -D &
nohup airflow scheduler &`
	
**Step 7 (Optional) : Setting up Airlow behind Nginx as Proxy Server**
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
- Run `python -m py_compile <your_python_script>`.
- Open Web UI to check your DAG.
