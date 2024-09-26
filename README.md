An automated system was set up to run tests in both normal and 
stressed (noisy) conditions. Using a CI toolchain on GitLab, 
Docker containers are launched on dedicated servers, with each 
server handling a specific test configuration to avoid interference. 
A noise tool was implemented in Python to introduce stress into the 
system, including resource-based stress (CPU, memory, etc.) using the 
stress-ng tool, and network-based noise, such as packet loss, using 
tcconfig. This setup ensures consistent and isolated testing environments
for accurate results.
