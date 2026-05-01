1. One EC2 in east and other one in south, how it will communication with one other ?

**Case 1: Both have pulic IP**

- So target EC2(north) should have below SG configuration, so you are able to access

```bash
ping <public-ip>
```

<img width="1918" height="715" alt="image" src="https://github.com/user-attachments/assets/af7924fe-7de8-41b2-ab67-262f40f6deb3" />

