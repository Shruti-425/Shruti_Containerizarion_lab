# **EXPERIMENT – 1**

## Virtual Machine and Container Setup

---

## **Part A: Virtual Machine using Vagrant (Windows)**

---

### **Step 1: Install VirtualBox**

Download and install VirtualBox from the official website:

🔗 [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

![Download VirtualBox](Images/Image1.png)

---

### **Step 2: Install Vagrant**

Download and install Vagrant from:

🔗 [https://developer.hashicorp.com/vagrant/install](https://developer.hashicorp.com/vagrant/install)

![Download Vagrant](Images/Image2.png)

---

### **Verify Vagrant Installation**

Check the installed version:

```bash
vagrant --version
```

![Vagrant Version](Images/Image3.png)

---

### **Step 3: Initialize Vagrant with Ubuntu Box**

Initialize Vagrant using Ubuntu:

```bash
vagrant init hashicorp/bionic64
```

![Vagrant Init](Images/Image4.png)

---

### **Start the Virtual Machine**

```bash
vagrant up
```

![Vagrant Up](Images/Image5.png)

---

### **Access the Virtual Machine**

```bash
vagrant ssh
```

![Vagrant SSH](Images/Image6.png)

---

### **Step 4: Install Nginx inside the VM**

```bash
sudo apt update
sudo apt install -y nginx
sudo systemctl start nginx
```

---

### **Verify Nginx Installation**

```bash
curl localhost
```

![Verify Nginx](Images/Image7.png)

---

### **Utilization Matrix (Running State)**

![Running State Matrix](Images/Image8.png)

---

### **Stop the Virtual Machine**

```bash
vagrant halt
```

![Vagrant Halt](Images/Image9.png)

---

### **Remove the Virtual Machine**

```bash
vagrant destroy
```

---

## **Part B: Container using WSL**

---

### **Step 1: Install WSL 2**

```bash
wsl --install
```

![WSL Install](Images/I10.png)

---

### **Step 2: Install Ubuntu on WSL**

```bash
wsl --install -d Ubuntu
```

![Ubuntu on WSL](Images/I11.png)

---

### **Step 3: Install Docker Engine inside WSL**

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo usermod -aG docker $USER
```

![Docker Install](Images/I12.png)
![Docker Service Start](Images/I13.png)
![Docker User Group](Images/I14.png)

>  Logout and login again to apply group changes.

---

### **Step 4: Run Ubuntu Container with Nginx**

```bash
docker pull nginx
docker run -d -p 8080:80 --name nginx-container nginx
```

![Docker Pull](Images/I15.png)
![Docker Run](Images/I17.png)

---

### **Step 5: Verify Nginx in Container**

```bash
curl localhost:8080
```

![Nginx in Container](Images/I16.png)

---

## **Result**

Virtual machines were successfully created using Vagrant and containers were deployed using Docker inside WSL. Nginx was installed and verified in both environments.

---