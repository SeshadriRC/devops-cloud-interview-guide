- Before Increase

<img width="891" height="468" alt="image" src="https://github.com/user-attachments/assets/2bedf6f2-fc06-4a7c-b0e6-08a02d4045d3" />

- Current volume size is 8 GB, now increase it to 30 GB. Once we modify, it will go to optimizing state, once done we can increase the size in vm.

<img width="1917" height="723" alt="image" src="https://github.com/user-attachments/assets/8f6a7a35-8e49-40a6-bf81-cb28881bf6d6" />
<img width="1914" height="679" alt="image" src="https://github.com/user-attachments/assets/abd34c87-487b-4f56-80a7-22e01339f53e" />
<img width="1919" height="386" alt="image" src="https://github.com/user-attachments/assets/97ade00e-561f-40f4-8f75-28eb1d36525f" />

- It came to `In-use` state, now we can resize.

<img width="1918" height="330" alt="image" src="https://github.com/user-attachments/assets/5cf63778-f4b1-49b6-8b52-fd9c4cabf25a" />

- Run the below command to resize

```bash
sudo apt install cloud-guest-utils
sudo growpart /dev/xvda 1
sudo resize2fs /dev/xvda1
```

- From the below image we can confirm that size is increased to 29G.

<img width="1784" height="960" alt="image" src="https://github.com/user-attachments/assets/42768fd0-8e39-4d96-af40-b2a07633908f" />


