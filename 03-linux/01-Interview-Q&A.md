# Networking

**How to check which port is in use and which process is using it**

```bash
root@controlplane:~$ netstat -tulnp | grep 9090

tcp        0      0 0.0.0.0:9090            0.0.0.0:*               LISTEN      7951/kubectl        
```
