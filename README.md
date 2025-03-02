# EC-Instance-Connect-to-docker-instance-Tomcat

This project to create an AWS EC2 Spot Instance (t3.medium) running Ubuntu, install Docker, deploy a Tomcat container.

---

## **Step 1: Launch an EC2 Instance**
1. Log in to the AWS Management Console.
2. Navigate to **EC2** > **Instances** > **Launch Instances**.
3. Configure the instance:
   - **Name**: Docker-Tomcat
   - **AMI**: Ubuntu Server (latest version)
   - **Instance Type**: t3.medium
   - **Key Pair**: Select or create a key pair for SSH access.
   - **Network Settings**: Assign a security group (e.g., `Launchwizard1`) that allows SSH (port 22) and HTTP (port 8081) access.
   - **Advanced Details**:
     - **Purchase Option**: Request Spot Instances.
4. Launch the instance.

---

## **Step 2: Connect to the EC2 Instance**
1. Once the instance is running, connect using **EC2 Instance Connect** or SSH:
   ```bash
   ssh -i your-key.pem ubuntu@<Public-IP>
   ```
2. Switch to the root user:
   ```bash
   sudo su
   ```

---

## **Step 3: Install Docker**
Run the following commands to install Docker:

1. Update the package list:
   ```bash
   sudo apt-get update
   ```

2. Install required packages:
   ```bash
   sudo apt-get install ca-certificates curl -y
   ```

3. Create a directory for Docker's keyring:
   ```bash
   sudo install -m 0755 -d /etc/apt/keyrings
   ```

4. Download and add Docker's GPG key:
   ```bash
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   ```

5. Add Docker's repository to the sources list:
   ```bash
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
     $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

6. Update the package list again:
   ```bash
   sudo apt-get update
   ```

7. Install Docker:
   ```bash
   sudo apt-get install docker-ce docker-ce-cli containerd.io -y
   ```

8. Verify Docker installation:
   ```bash
   sudo docker --version
   ```

9. Test Docker with a hello-world container:
   ```bash
   sudo docker run hello-world
   ```

10. Enable and start Docker service:
    ```bash
    sudo systemctl enable docker
    sudo systemctl start docker
    sudo systemctl status docker
    ```

---

## **Step 4: Deploy Tomcat Container**
1. Pull the Tomcat image:
   ```bash
   sudo docker pull tomcat
   ```

2. Run a Tomcat container:
   ```bash
   sudo docker run -d --name tomcat-container -p 8081:8080 tomcat
   ```

3. Verify the container is running:
   ```bash
   sudo docker ps
   ```

---

## **Step 5: Access Tomcat**
1. Open a browser and navigate to:
   ```
   http://<Public-IP>:8081
   ```
   - If you see an error, proceed to the next step.

---

## **Step 6: If any root error ,resolve Tomcat Access Error**
1. Log in to the Tomcat container:
   ```bash
   sudo docker exec -it tomcat-container bash
   ```

2. Navigate to the `webapps.dist` directory:
   ```bash
   cd /usr/local/tomcat/webapps.dist
   ```

3. Copy contents to the `webapps` directory:
   ```bash
   cp -R * ../webapps/
   ```

4. Exit the container:
   ```bash
   exit
   ```

5. Refresh the browser and access Tomcat again:
   ```
   http://<Public-IP>:8081
   ```

---

## **Step 7: Verify Setup**
1. Check the status of the Tomcat container:
   ```bash
   sudo docker ps
   ```

2. Ensure the Tomcat welcome page is accessible in the browser.

---

## **Troubleshooting**
- **Docker not running**: Restart Docker:
  ```bash
  sudo systemctl restart docker
  ```
- **Port 8081 not accessible**: Ensure the security group allows inbound traffic on port 8081.
- **Tomcat still not working**: Restart the container:
  ```bash
  sudo docker restart tomcat-container
  ```

---

## **Conclusion**
Set up a Docker instance on an AWS EC2 , deployed a Tomcat container. Enjoy using Tomcat on Docker!
