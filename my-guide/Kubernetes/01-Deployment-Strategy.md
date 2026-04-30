# Deployment Strategies

## Blue-Green Deployment

. You have two identical environments

o Blue > Current live version

o Green > New version

. Deploy new verson to Green
. Switch traffic instantly

<img width="349" height="191" alt="image" src="https://github.com/user-attachments/assets/109ff183-d610-46ba-b16f-61f8399b3b0f" />


Before switch: Traffic --> Blue
After switch: Traffic --> Green


**Pros**

. Instant rollback
. No downtime

**Cons**
- Cost (double infrastructure)

---
## Rolling Deployment

- Update servers one by one
- Some run old version, some run new

**Before:**
[V1][V1][V1][V1]

**During**
[V2][V1][V1][V1]
[V2][V2][V1][V1]

**After**
[V2][V2][V2][V2]

<img width="300" height="233" alt="image" src="https://github.com/user-attachments/assets/d92c3e94-8f7d-4724-ac51-7f783e814d4c" />

**Pros**
- No extra infrastructure needed
- Smooth transition

**Cons**
- Temporary mixed versions
- Harder rollback

---
## Canary Deployment

- Release to small % of users first
- Monitor --> then increase traffic gradually

<img width="311" height="148" alt="image" src="https://github.com/user-attachments/assets/3afa4654-8010-4ee5-8089-fcc7aefb36e3" />

**Pros**

- Safer releases
- Real user testing

**Cons**

- Requires monitoring setup
  
---
## Recreate Deployment

- Stop old version --> Start new version
- Causes downtime --> No Zero downtime

---

### How do you achieve zero downtime deployments?
I typically use strategies like Blue-Green, Rolling, and Canary deployments depending on the system requirements.

- For critical systems --> Blue-Green
- For cost efficiency --> Rolling
- For risk mitigation --> Canary

I also ensure health checks, readiness probes, and load balancer routing are properly configured to avoid downtime.

---
