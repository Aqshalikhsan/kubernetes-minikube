# Kubernetes Basic Setup & Learning Guide

<p align="center">
  <img width="714" height="751" alt="0109-kubernetes-tools-ecosystem" src="https://github.com/user-attachments/assets/af24f2a0-0740-4269-995f-8f1c8897bc5f" />
</p>

> **Catatan Penting:** Sebelum memulai, **buka Docker Desktop terlebih dahulu** dan pastikan statusnya **Running**.  
> Jika Docker belum aktif, Minikube tidak akan bisa berjalan.

---

# Daftar Isi

1. Apa itu Kubernetes
2. Cara Install Kubernetes
3. Setup Kubernetes di Windows (Minikube)
4. Struktur Project
5. Deployment & Service
6. Menjalankan YAML
7. Monitoring Pod
8. Scale Up & Scale Down
9. Menambah Worker Node
10. Menghapus Project / Reset
11. Basic Command Kubernetes
---


# 1. Apa itu Kubernetes

Kubernetes adalah platform untuk mengelola container secara otomatis, seperti:

- Menjalankan aplikasi container
- Menjaga aplikasi tetap hidup
- Load balancing
- Scaling otomatis
- Self healing
- Deploy multi server

---

# 2. Cara Install Kubernetes

Ada banyak cara install Kubernetes sesuai kebutuhan:

## Opsi 1 — Docker Desktop Kubernetes
Cocok untuk user Windows yang ingin cepat mencoba.

## Opsi 2 — Linux via MicroK8s (**paling bagus untuk Linux**)
Ringan dan stabil.

## Opsi 3 — Windows via Minikube (**paling bagus untuk belajar di Windows**)
Paling mudah dan fleksibel.

---

# 3. Setup Kubernetes di Windows (Minikube)

## Langkah 1 — Buka PowerShell

Pastikan Docker Desktop sudah Running.

## Install Minikube

```powershell
winget install Kubernetes.minikube
````

## Jalankan Minikube

```powershell
minikube start --driver=docker
```

## Cek Node

```powershell
kubectl get nodes
```

Jika berhasil:

```text
minikube   Ready   control-plane
```

---

# 4. Struktur Project

Buat folder project:

```powershell
mkdir kubernetes
cd kubernetes
code .
```

---

# 5. Deployment & Service

## Penjelasan

### Deployment

Berfungsi untuk:

* Menjalankan container
* Menentukan jumlah replica
* Auto restart jika crash
* Update aplikasi
* Scale up/down

### Service

Berfungsi untuk:

* Memberi akses ke aplikasi
* Load balancing ke pod
* Menjadi pintu masuk traffic

---

# 6. File YAML

## deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-nginx
  template:
    metadata:
      labels:
        app: web-nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

---

## service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-nginx-service
spec:
  selector:
    app: web-nginx
  ports:
    - port: 80
      targetPort: 80
  type: NodePort
```

---

# 7. Menjalankan YAML

## Apply File

```powershell
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

## Lihat Semua Resource

```powershell
kubectl get all
```

## Cek Pod

```powershell
kubectl get pods
```

## Monitoring Live

```powershell
kubectl get pods -w
```

---

# 8. Jika Pod Stuck Lama

Jika status `ContainerCreating` terlalu lama:

```powershell
kubectl describe pod nama-pod
```

Contoh:

```powershell
kubectl describe pod web-nginx-6798694bb6-2qqqj
```

> Copy paste nama pod sesuai milikmu.

---

# 9. Membuka Aplikasi

```powershell
minikube service web-nginx-service
```

Lalu buka link IP yang muncul di browser.

---

# 10. Scaling Pod

## Lihat Pod Live

Terminal 1:

```powershell
kubectl get pods -w
```

## Scale Up ke 5

Terminal 2:

```powershell
kubectl scale deployment web-nginx --replicas=5
```

## Scale Down ke 3

```powershell
kubectl scale deployment web-nginx --replicas=3
```

---

## Lihat Deployment Live

Terminal lain:

```powershell
kubectl get deployment -w
```

---

# 11. Menambah Worker Node

## Tambah Worker Node

```powershell
minikube node add
```

## Cek Node

```powershell
kubectl get nodes
```

## Status Cluster

```powershell
minikube status
```

---

# 12. Menghapus Project

## Hapus Deployment & Service

```powershell
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

---

# 13. Reset Cluster Jika Error

```powershell
minikube delete
```

Lalu jalankan ulang:

```powershell
minikube start --driver=docker
```

---

# 14. Basic Command Kubernetes

## Node

```powershell
kubectl get nodes
```

Menampilkan node cluster.

---

## Pod

```powershell
kubectl get pods
```

Menampilkan semua pod.

```powershell
kubectl get pods -o wide
```

Menampilkan pod + node tempat berjalan.

---

## Deployment

```powershell
kubectl get deployment
```

Melihat deployment aktif.

```powershell
kubectl scale deployment web-nginx --replicas=5
```

Mengubah jumlah pod.

---

## Service

```powershell
kubectl get svc
```

Melihat service.

---

## Semua Resource

```powershell
kubectl get all
```

---

## Describe Detail

```powershell
kubectl describe pod nama-pod
kubectl describe deployment web-nginx
kubectl describe service web-nginx-service
```

---

## Logs

```powershell
kubectl logs nama-pod
```

Melihat log container.

---

## Delete

```powershell
kubectl delete pod nama-pod
kubectl delete deployment web-nginx
kubectl delete service web-nginx-service
```

---

# 15. Alur Kubernetes

```text
User Request
   ↓
Service
   ↓
Pod 1
Pod 2
Pod 3
```

Pod dibuat dan dijaga oleh Deployment.

---
> **Notes Penting:** Kubernetes **tidak hanya digunakan untuk Service dan Deployment saja**.  
>  Fungsi lain: 
> - **ReplicaSet** → menjaga jumlah pod tetap sesuai kebutuhan  
> - **Namespace** → memisahkan resource dalam cluster  
> - **ConfigMap** → menyimpan konfigurasi aplikasi  
> - **Secret** → menyimpan data sensitif (password, token, key)  
> - **Volume / PersistentVolume** → penyimpanan data permanen  
> - **Ingress** → akses aplikasi menggunakan domain / routing HTTP  
> - **HPA (Horizontal Pod Autoscaler)** → scaling otomatis berdasarkan CPU/RAM  
> - **DaemonSet** → menjalankan pod di setiap node  
> - **StatefulSet** → workload yang butuh identitas tetap (database, redis, dll)  
> - **Job / CronJob** → menjalankan task sekali jalan atau terjadwal  
> - **Node Management** → control-plane, worker node, scheduling  
> - **Monitoring** → Prometheus, Grafana, Metrics Server  
> - **Security & RBAC** → role, permission, access control  

