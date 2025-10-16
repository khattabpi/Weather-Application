# ☁️ WeatherApp on Kubernetes

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED.svg?style=for-the-badge&logo=docker&logoColor=white)
![NGINX](https://img.shields.io/badge/NGINX-009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)

A cloud-native **Weather Application** deployed on a **Kubernetes cluster** using a microservices architecture.  
This project showcases container orchestration, service communication, and ingress routing with **NGINX**, emphasizing a modular and scalable design.

---

## 🚀 Project Overview

The **WeatherApp** is built using a **microservices architecture**, consisting of the following components:

1. **UI (Frontend)** – React-based web interface served via NGINX.  
2. **Auth Service** – Manages user authentication and login requests.  
3. **Weather Service** – Delivers real-time weather data.  
4. **MySQL Database** – Stores user credentials and application data.

All components are containerized with **Docker** and orchestrated on **Kubernetes** using **Deployments**, **Services**, and an **NGINX Ingress Controller** with **TLS** enabled.

---

## 🏗️ Architecture

```plaintext
+-----------------------+
|     weatherapp-ui     |  <--->  Ingress (NGINX)
+-----------------------+
          |
          v
+-----------------------+
|   weatherapp-auth     |
+-----------------------+
          |
          v
+-----------------------+
|  weatherapp-weather   |
+-----------------------+
          |
          v
+-----------------------+
|        MySQL          |
+-----------------------+
```

---

## ⚙️ Technologies Used

- **Kubernetes** (using `kind` for local clusters)
- **Docker** for containerization
- **NGINX Ingress Controller** for routing
- **MySQL** for persistent storage
- **TLS Certificates** for secure HTTPS
- **YAML Manifests** for Kubernetes resources
- **cURL** for testing endpoints

---

## 🧩 Kubernetes Resources

| Resource Type    | Name                        | Description                              |
|-------------------|-----------------------------|------------------------------------------|
| **Deployment**    | `weatherapp-ui`            | React frontend pods                     |
| **Deployment**    | `weatherapp-auth`          | Authentication backend                  |
| **Deployment**    | `weatherapp-weather`       | Weather API backend                     |
| **StatefulSet**   | `mysql`                    | Database backend                        |
| **Service**       | `weatherapp-ui`            | Internal service for UI                 |
| **Service**       | `weatherapp-auth`          | Internal service for Auth               |
| **Service**       | `weatherapp-weather`       | Internal service for Weather            |
| **Ingress**       | `weatherapp-ui-ingress`    | Entry point for external traffic        |
| **Secret**        | `weatherapp-ui-tls`        | TLS certificate for HTTPS               |

---

## 🧠 How to Deploy

### 1️⃣ Create a Kubernetes Cluster
If using **kind**:
```bash
kind create cluster --name weatherapp
```

### 2️⃣ Deploy All Resources
Apply manifests from the project folder:
```bash
kubectl apply -f k8s/
```

### 3️⃣ Verify Deployments
Check that all pods are running:
```bash
kubectl get pods
```

**Expected Output:**
```
NAME                                    STATUS
mysql-0                               Running
weatherapp-auth-849f6bfb88-4wt7l      Running
weatherapp-ui-6cb8c68c45-2q5pp        Running
weatherapp-weather-54cf785766-dpnn5   Running
```

### 4️⃣ Verify Services
```bash
kubectl get svc
```

**Expected Output:**
```
NAME                   TYPE        CLUSTER-IP      PORT(S)
weatherapp-ui         ClusterIP   10.96.76.84     3000/TCP
weatherapp-auth       ClusterIP   10.96.77.6      8080/TCP
weatherapp-weather    ClusterIP   10.96.48.160    5000/TCP
```
<img width="1860" height="178" alt="Screenshot from 2025-10-16 21-53-19" src="https://github.com/user-attachments/assets/e3705d29-c68c-467f-b077-9fb4597df8f3" />

### 5️⃣ Check Ingress
```bash
kubectl get ingress
```

**Expected Output:**
```
NAME                    CLASS   HOSTS              ADDRESS     PORTS     AGE
weatherapp-ui-ingress   nginx   weatherapp.local   localhost   80, 443   ...
```

### 6️⃣ Add Host Entry
Edit `/etc/hosts` to map the domain:
```bash
sudo nano /etc/hosts
```
Add:
```
127.0.0.1 weatherapp.local
```

### 7️⃣ Access the App
Open your browser at:
```
https://weatherapp.local
```

Or test with cURL:
```bash
curl -k https://weatherapp.local
```
<img width="921" height="597" alt="Screenshot from 2025-10-17 00-53-08" src="https://github.com/user-attachments/assets/8b6dae10-1e85-45f8-84ea-2f3a38e26ebd" />

---

## 🔐 HTTPS Configuration

A **self-signed TLS certificate** is used to secure traffic via HTTPS.

Create the TLS secret:
```bash
kubectl create secret tls weatherapp-ui-tls \
  --cert=weatherapp.local.crt \
  --key=weatherapp.local.key
```

Reference it in the ingress manifest:
```yaml
tls:
  - hosts:
      - weatherapp.local
    secretName: weatherapp-ui-tls
```

---

## 🧰 Useful Commands

| Command                                            | Description                              |
|----------------------------------------------------|------------------------------------------|
| `kubectl get all`                                  | List all resources in the namespace      |
| `kubectl logs <pod-name>`                          | View logs for a specific pod             |
| `kubectl describe ingress weatherapp-ui-ingress`   | Inspect ingress details                  |
| `kubectl port-forward svc/weatherapp-ui 3000:3000` | Access service locally                   |
| `curl -k https://weatherapp.local`                 | Test app over HTTPS                      |

---

## 🧾 Example Output Screenshots

| Component       | Status                                                           |
|-----------------|------------------------------------------------------------------|
| **Pods**        | ✅ Running                                                       |
| **Services**    | ✅ Exposed                                                       |
| **Ingress**     | ✅ Configured with TLS                                           |
| **Browser**     | ✅ Accessible at [https://weatherapp.local](https://weatherapp.local) |

---

## 🧱 Folder Structure

```
kubernetes-lab/
│
├── auth/
│   ├── deployment.yaml
│   ├── service.yaml
│
├── weather/
│   ├── deployment.yaml
│   ├── service.yaml
│
├── ui/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│
└── mysql/
    ├── statefulset.yaml
    ├── service.yaml
```

---

## 🧭 Troubleshooting Tips

- **Pods stuck in `ContainerCreating`**:
  Check image pull issues:
  ```bash
  kubectl describe pod <pod-name>
  ```

- **503 Service Temporarily Unavailable**:
  Ensure:
  - `weatherapp-ui` pods are running.
  - Ingress points to correct service name/port.
  - TLS secret exists and matches ingress configuration.

- **Restart pods**:
  ```bash
  kubectl rollout restart deployment/weatherapp-ui
  ```

---

## 🧩 Future Improvements

- Add **Grafana + Prometheus** for monitoring.
- Implement a **CI/CD pipeline** with GitHub Actions.
- Create a **Helm chart** for simplified deployments.
- Integrate **ExternalDNS** and **Cert-Manager** for automated DNS and TLS.

---

## 👨‍💻 Author

**Abdulrahman Khattab**  
📍 Telecommunications & Electronics Engineer  
💼 Passionate about Cloud, DevOps, and Kubernetes technologies  
🌐 [LinkedIn]([https://www.linkedin.com](https://www.linkedin.com/in/abdulrahman-khattab-pi/)) | [GitHub]([https://github.com](https://github.com/khattabpi))

---

## 🏁 Result

✅ The **WeatherApp** is successfully deployed and accessible at:  
👉 **[https://weatherapp.local](https://weatherapp.local)**

All components are running, connected, and serving traffic securely via **NGINX Ingress** with **HTTPS**.
