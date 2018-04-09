##Add new dag
**-Step 1:** copy <your_dag_file>.py into server by running `scp <your_dag_file> <your_username>@jupyter.ghn.vn:/home/<your_username>/`
**-Step 2:** ssh into server by `ssh <your_username>@jupyter.ghn.vn`
**-Step 3:** switch to root user by `sudo su`
**-Step 4:** copy your dag file  `cp /home/<your_username>/<your_dag_file> /root/airflow/dags/`
**-Step 5:** `cd /root/airflow/dags`
**-Step 6:** run `source /root/minhlg/bin/active`
**-Step 7:** run `sh ../restart_airflow.sh`. Wait for 3 seconds then press Enter
**-Step 8:** go to Airflow UI and refresh to check your dag it up or not.


