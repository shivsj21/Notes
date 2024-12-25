### Update and Install HAProxy & Check HAProxy Version
```sh
sudo apt update
sudo apt install haproxy -y
haproxy -v
```

### Run two backend services (for testing):
- Open two terminal windows and run these commands in each:

#### First terminal:
```sh
python3 -m http.server 8081
```
#### Second terminal:
```sh
python3 -m http.server 8082
```
- Instructions to set up two simple HTTP servers on ports 8081 and 8082.

### Edit HAProxy Configuration File  
```sh
   sudo vim /etc/haproxy/haproxy.cfg
```

5. **Verify HAProxy Configuration Syntax**  
   Command to check the HAProxy configuration for syntax errors.








	

// In this configuration:
//server1 is running on port 8081 of the same machine (127.0.0.1 is the loopback IP).
//server2 is running on port 8082 of the same machine.

	
//To add last in the file.
	frontend http_front
	    bind *:80
	    default_backend http_back

	backend http_back
    	    balance roundrobin
    	    server server1 127.0.0.1:8081 check
            server server2 127.0.0.1:8082 check
            
=> Check HAProxy Configuration syntax
            haproxy -c -f /etc/haproxy/haproxy.cfg

### Restart HAProxy Service
```sh
sudo systemctl restart haproxy
```

Test HAProxy Load Balancing
=> Test the Configuration:
	Now, open a browser and go to http://192.168.1.6. 
	HAProxy will balance requests between the two services running on ports 8081 and 8082.
	
### Remove HAProxy
```sh
sudo apt remove --purge haproxy
```
