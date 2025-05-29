## **1. Perbandingan Application Logs Sebelum dan Sesudah Exposed sebagai Service**

### **Sebelum di-expose sebagai Service:**
- Logs hanya menampilkan pesan startup server:
```
kubectl logs hello-node-c74958b5d-88w9w
I0529 07:13:07.159995       1 log.go:195] Started HTTP server on port 8080
I0529 07:13:07.160389       1 log.go:195] Started UDP server on port  8081
I0529 07:23:03.075226       1 log.go:195] GET /
I0529 07:23:07.277755       1 log.go:195] GET /
```
- Tidak ada log tambahan karena aplikasi belum bisa diakses dari luar

### **Sesudah di-expose sebagai Service dan diakses berkali-kali:**
- **Ya, jumlah logs bertambah** setiap kali aplikasi dibuka
- Muncul log baru yang mencatat setiap HTTP request:
```
kubectl logs hello-node-c74958b5d-88w9w
I0529 07:13:07.159995       1 log.go:195] Started HTTP server on port 8080
I0529 07:13:07.160389       1 log.go:195] Started UDP server on port  8081
I0529 07:23:03.075226       1 log.go:195] GET /
I0529 07:23:07.277755       1 log.go:195] GET /
I0529 07:44:30.433662       1 log.go:195] GET /
I0529 07:44:31.710927       1 log.go:195] GET /
I0529 07:44:33.549815       1 log.go:195] GET /
I0529 07:44:33.580960       1 log.go:195] GET /
I0529 07:44:34.817335       1 log.go:195] GET /
I0529 07:44:34.853359       1 log.go:195] GET /
I0529 07:44:35.588229       1 log.go:195] GET /
I0529 07:44:35.641369       1 log.go:195] GET /
I0529 07:44:36.237177       1 log.go:195] GET /
I0529 07:44:36.279405       1 log.go:195] GET /
```

**Kesimpulan:** Setiap kali browser mengakses aplikasi melalui Service, server mencatat aktivitas tersebut dalam logs.

---

## **2. Perbedaan `kubectl get` dengan dan tanpa opsi `-n`**

### **Tanpa opsi `-n`:**
```shell
kubectl get pods
kubectl get services
```
- Menampilkan resources di **namespace default**
- Hanya menampilkan pods/services yang dibuat sendiri (hello-node)

### **Dengan opsi `-n kube-system`:**
```shell
kubectl get pods -n kube-system
kubectl get services -n kube-system
```

### **Tujuan opsi `-n`:**
- **`-n` = namespace** (menentukan namespace mana yang ingin dilihat)
- **`kube-system`** = namespace khusus untuk komponen sistem Kubernetes

### **Mengapa output tidak menampilkan pods/services yang dibuat?**
Karena:
- Pods/services yang dibuat ada di **namespace "default"**
- Perintah dengan `-n kube-system` hanya menampilkan yang ada di namespace **"kube-system"**
- Namespace **kube-system** berisi komponen internal Kubernetes seperti:
  - `coredns` (DNS server)
  - `etcd` (database cluster)
  - `kube-apiserver` (API server)
  - `metrics-server` (monitoring)

**Analogi:** Namespace seperti "folder" yang memisahkan resources. Aplikasi ada di folder "default", sedangkan sistem Kubernetes ada di folder "kube-system".