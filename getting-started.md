Credit to: https://i12bretro.github.io/tutorials/0845.html

## Self-Hosting Bitwarden Password Vault with Docker

**What is Bitwarden?**

Bitwarden is a free/freemium open-source password management service that stores sensitive information like website credentials in an encrypted vault. It offers various client applications, including a web interface, desktop apps, browser extensions, mobile apps, and a command-line interface. Bitwarden provides a free cloud-hosted service alongside the ability to self-host. - [https://en.wikipedia.org/wiki/Bitwarden](https://en.wikipedia.org/wiki/Bitwarden) 

**Installing Docker**

1. **Log in** to your Linux-based device.
2. Run the following commands in the terminal:
   - install prerequisites
   ```bash
   sudo apt install apt-transport-https ca-certificates curl software-properties-common gnupg-agent -y
   ```
   - add docker gpg key
   ```bash
   curl -fsSL https://download.docker.com/linux/$(awk -F'=' '/^ID=/{ print $NF }' /etc/os-release)/gpg | sudo apt-key add -
   ```
   - add docker software repository
   ```bash
   sudo add-apt-repository "deb [arch=$(dpkg --print-architecture)] https://download.docker.com/linux/$(awk -F'=' '/^ID=/{ print $NF }' /etc/os-release) $(lsb_release -cs) stable"
   ```
   - install docker
   ```bash
   sudo apt install docker-ce docker-compose containerd.io -y
   ```
   - enable and start docker service
   ```bash
   sudo systemctl enable docker && sudo systemctl start docker
   ```
   - add the current user to the docker group
   ```bash
   sudo usermod -aG docker $USER
   ```
   - reauthenticate for the new group membership to take effect
   ```bash
   su - $USER
   ```

**Running Bitwarden Containers**

**Note:** To verify DNS name ownership by Let's Encrypt, the Docker host needs to be accessible via ports 80 (http) and 443 (https). Homelab users typically achieve this through port forwarding on their router (beyond this tutorial's scope).

1. Open a web browser and navigate to [https://bitwarden.com/help/self-host-an-organization/](https://bitwarden.com/help/self-host-an-organization/)
2. Enter an email address and click "Submit."
3. Copy the Installation ID and Key for later use.
4. Continue with the following commands in a terminal window:
   - create a working directory
   ```bash
   mkdir ~/docker/bitwarden -p
   ```
   - create a bitwarden user account
   ```bash
   sudo adduser bitwarden --disabled-password
   ```
   - add the bitwarden user to the docker group
   ```bash
   sudo usermod -aG docker bitwarden
   ```
   - create bitwarden install directory
   ```bash
   sudo mkdir /opt/bitwarden
   ```
   - set permission on the install directory
   ```bash
   sudo chmod -R 700 /opt/bitwarden
   ```
   - set ownership of install directory to bitwarden
   ```bash
   sudo chown -R bitwarden:bitwarden /opt/bitwarden
   ```
   - cd into the working directory
   ```bash
   cd ~/docker/bitwarden
   ```
   - download the bitwarden installation script
   ```bash
   curl -Lso bitwarden.sh https://go.btwrdn.co/bw-sh
   ```
   - make the install script executable
   ```bash
   chmod 700 bitwarden.sh
   ```
   - execute the installation scripts
   ```bash
   ./bitwarden.sh install
   ```

   - When prompted, enter a domain name for your Bitwarden installation.
   - Choose whether to use Let's Encrypt for SSL certificates.
   - Enter a database name for your Bitwarden instance.
   - Enter the Installation ID obtained earlier.
   - Enter the Installation Key obtained earlier.
   - Choose whether you have an SSL certificate to use. (If not, choose to generate a self-signed certificate.)

**Configure SMTP and Admin Settings**

1. Edit the `.env` file:

   ```bash
   nano ~/docker/bitwarden/bwdata/env/global.override.env
   ```

2. Update the SMTP host configuration and optionally add admin email addresses:

   ```
   globalSettings__mail__replyToEmail=no-reply@yourdomain.local  # Replace with your domain

   globalSettings__mail__smtp__host=smtp.yourdomain.local     # Replace with your domain

   globalSettings__mail__smtp__port=25

   globalSettings__mail__smtp__ssl=false

   globalSettings__mail__smtp__username=bitwarden@yourdomain.local # Replace with your domain

   globalSettings__mail__smtp__password=  # Your password (leave blank if not used)

   adminSettings__admins=youremail@yourdomain.local            # Replace with your email address
   ```

**Restart Bitwarden and Access the Interface**

1. Restart Bitwarden containers:

   ```bash
   ~/docker/bitwarden/bitwarden.sh restart
   ```

2. Open a web browser and navigate to `https://your_domain_or_IP` (replace with your domain or IP address).
3. Click "Create Account."
4. Fill out the form with your email address, name, and master password. Click "Create Account."
5. Log in to Bitwarden using the email address and password you set earlier.

Congratulations! You've successfully self-hosted Bitwarden and can now manage your passwords securely.

Documentation: https://bitwarden.com/help/install-on-premise-linux/#post-install-configuration

Source: https://bitwarden.com/help/install-on-premise-linux/
