### Update and Install HAProxy & Check HAProxy Version
```sh
sudo apt update
sudo apt install haproxy -y
haproxy -v
```

3. **Run Backend Services for Testing**  
   Instructions to set up two simple HTTP servers on ports 8081 and 8082.

4. **Edit HAProxy Configuration File**  
   Steps to configure HAProxy with frontend and backend settings.

5. **Verify HAProxy Configuration Syntax**  
   Command to check the HAProxy configuration for syntax errors.

6. **Restart HAProxy Service**  
   Command to restart the HAProxy service to apply changes.

7. **Test HAProxy Load Balancing**  
   Steps to test load balancing by accessing the configured IP in a browser.

8. **Remove HAProxy (Optional)**  
   Command to remove HAProxy and clean up the installation.






### Run two backend services (for testing):
- Open two terminal windows and run these commands in each:

## First terminal:
```sh
python3 -m http.server 8081
```
	=> Second terminal:
		python3 -m http.server 8082

//These commands will run two simple HTTP servers on ports 8081 and 8082.




=> Configure HAProxy:

	sudo vim /etc/haproxy/haproxy.cfg

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

            
=> Restart HAProxy:
	sudo systemctl restart haproxy


=> Test the Configuration:
	Now, open a browser and go to http://192.168.1.6. 
	HAProxy will balance requests between the two services running on ports 8081 and 8082.
	
	
	sudo apt remove --purge haproxy
