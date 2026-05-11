- Follow this [doc](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

```bash
# Add ubuntu user to Docker group, then logout and login
sudo usermod -aG docker ubuntu

# Now test without using sudo
docker ps
```
