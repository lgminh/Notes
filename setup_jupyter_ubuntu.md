# Setting up Jupyter on Ubuntu 16.04.04

**Step 1:** 
Open terminal then run `pip install jupyter`

**Step 2:** 
This step is **optional**. You can skip this step if you want the default setting of jupyter.
You can set working folder of jupyter to keep all notebooks and base_url to access  notebooks from Web UI.

1. Set up config  
    - Run `juyter notebook --generate-config`.
    
2. Set up base url 
    - Open `/root/.jupyter/jupter_notebook_config.py`.
    - Uncomment the line the line `c.NotebookApp.base_project_url` 
    and change it into `c.NotebookApp.base_project_url=<your_working_folder_path>`.

3. Set up base url
    - Open `/root/.jupyter/jupter_notebook_config.py`.
    - Uncomment the line the line `c.NotebookApp.base_url` 
    and change it into `c.NotebookApp.base_project_url=<your_url>`.

**Step 3:**
    -Run `jupyter notebook -p <port>` to start jupyter. If you want to use default port of jupyter then just run `jupyter notebook`. 
    -If you want to run **notebook in background**, first install **nohup** then run `nohup jupyter notebook -p <port> &`.
    - Open web browser and try accessing `localhost:<jupyter_port>` to check if jupyter is running or not.

# Set up Jupyter behind Nginx as Proxy Server
- This instruction is considered to be followed if you set up Jupyter behind **Nginx as Proxy Server**.
	
	-  Install Nginx 
	
	-  Install Jupyter

	-  Create `/etc/nginx/con.d/<your_file_name>.conf`
	
	- Set the [server block](https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms)  in your created file. To apply this config, first you must use **Nginx HTTP** and **Nginx HTTPS** firewall rule by running `ufw allow 'Nginx HTTP'` and `ufw allow 'Nginx HTTPS'`.
	
	```
		upstream jupyter {
	         server 127.0.0.1:<jupyter_port> fail_timeout=0;
        }

	   server {
                listen 80 default_server;
                listen 443 ssl default_server;
                server_name <your_server_name>;
	             
                ssl_certificate <your_crt_file_path>;
                ssl_certificate_key <your_key_file_path>;
                ssl_stapling on; # Requires nginx >= 1.3.7
                ssl_stapling_verify on; # Requires nginx => 1.3.7
                resolver_timeout 5s;
               
                location /<your_base_url> {
                        proxy_pass http://jupyter>;
                }

                location = /<your_base_url> {
                         rewrite ^/(.*)$ $1/ permanent;
                }

                location ~/<your_base_url> {
                        proxy_pass http://jupyter;
                        proxy_http_version      1.1;
                        proxy_set_header Host $host;
                        proxy_set_header Upgrade "websocket";
                        proxy_set_header Connection "Upgrade";
                }

                location ~ /jupyter/api/kernels/ {
                        proxy_pass            http://jupyter;
                        proxy_set_header      Host $host;
                        # websocket support
                        proxy_http_version    1.1;
                        proxy_set_header      Upgrade "websocket";
                        proxy_set_header      Connection "Upgrade";                                                                                                                                                                                                             
				}
	                location ~ /jupyter/terminals/ {
                        proxy_pass            http://jupyter;
                        proxy_set_header      Host $host;
                        # websocket support
                        proxy_http_version    1.1;
                        proxy_set_header      Upgrade "websocket";
                        proxy_set_header      Connection "Upgrade";
                        proxy_read_timeout    86400;
                }
```

	- Reload nginx `systemctl reload nginx`. 
