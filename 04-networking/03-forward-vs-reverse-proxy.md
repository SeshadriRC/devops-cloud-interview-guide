## Question  
Explain the difference between a Forward Proxy and a Reverse Proxy.

### 📝 Short Explanation  
Both proxies act as intermediaries in network communication, but they sit at **different ends of the request flow** and serve **different purposes**.

## ✅ Answer  

| Aspect              | Forward Proxy                            | Reverse Proxy                             |
|---------------------|------------------------------------------|--------------------------------------------|
| **Position**         | Between **client** and the internet       | Between **internet** and the **server**     |
| **Who it represents**| Acts **on behalf of the client**         | Acts **on behalf of the server**           |
| **Use Cases**        | - Anonymize clients  
                      - Bypass firewalls  
                      - Control access (e.g., parental control)  
                      - Caching outgoing requests  
                      | - Load balancing  
                      - SSL termination  
                      - Caching incoming responses  
                      - Protect internal servers |
| **Example**          | Client → Forward Proxy → Google          | User → Reverse Proxy → Internal Web Server |

---

### 📘 Detailed Explanation

#### 🔁 **Forward Proxy**

- A forward proxy is **used by clients** to access external servers.
- The target server only sees the proxy, **not the real client**.
- Often used in corporate environments or VPNs to **filter or restrict internet access**.

🧠 **Real-world analogy**:  
> Like a travel agent booking your ticket on your behalf — the airline doesn’t know who you are.

---

#### 🔃 **Reverse Proxy**

- A reverse proxy **sits in front of a group of servers** and routes client requests to them.
- The client thinks it's talking directly to the server, but it's talking to the proxy.
- Used for **load balancing**, **security**, and **SSL offloading**.

🧠 **Real-world analogy**:  
> Like a receptionist at an office — you talk to them, and they connect you to the right person inside.

---

### ✅ Example Scenarios

- **Forward Proxy**:  
  A school uses a proxy server to prevent students from accessing YouTube.

- **Reverse Proxy**:  
  A company uses NGINX as a reverse proxy to distribute requests to multiple backend services (e.g., `/api`, `/auth`, `/app`).

  <img width="1755" height="684" alt="image" src="https://github.com/user-attachments/assets/6501b79d-a919-4030-bd50-87022c8abb8a" />


---

> Summary:  
> A **Forward Proxy** serves the **client** and hides them from the server. A **Reverse Proxy** serves the **server** and hides it from the client. Their goals, position, and use cases are completely different — but both add control and flexibility to network traffic.

---
