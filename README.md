
**Steps for configuring a new local dev-box environment**

**System Requirements**

1. Operating System - Windows 11 22H2 | macOS
2. VirtualBox 7.1.4 (It should be a fresh installation. If it already exists, uninstall it completely and install a fresh copy.)
3. Vagrant 2.4.5+
4. AWS CLI (latest version)
5. GitHub Personal Access Token (PAT - fine-grained token)

**Create the GitHub PAT token**

* To create a new PAT token, follow the steps provided here:

  ```bash
  https://id-pal.monday.com/boards/1735082033/pulses/5797018445
  ```

**Install WSL and Ubuntu**

* Follow the instructions to install the Windows Subsystem and update to WSL2.

  ```
  https://learn.microsoft.com/en-us/windows/wsl/install
  ```

* Once WSL is installed, it will automatically install Ubuntu under WSL. However, we need to install a minimum of Ubuntu 22.04 version or another latest LTS Ubuntu version (e.g., 24.04 LTS) from the Microsoft Store.

* To install Ubuntu, open the Microsoft Store and type "Ubuntu" in the search bar. It will list the available Ubuntu versions.

* Click on ‘Get’ and it will start installing Ubuntu on your local machine.

* Once the Ubuntu Linux terminal is installed, open PowerShell in administrator mode and run the following command to verify that the Linux distribution is properly installed. Take note of the 'Name' and 'Version' of the distribution.

  ```
  wsl --list --verbose
  ```

  Output Example:

  | **NAME**     | **STATE** | **VERSION** |
  | ------------ | --------- | ----------- |
  | Ubuntu       | Running   | 2           |
  | Ubuntu-22.04 | Running   | 2           |

* Run the following command to set your distribution to run as WSL1. WSL must be updated to WSL2, but the distribution should be set to WSL1.

  ```
  wsl --set-version Ubuntu-22.04 1
  ```
* Now list the available Ubuntu version
  ```
  wsl --list --verbose
  ```

  Output Example:

  | **NAME**     | **STATE** | **VERSION** |
  | ------------ | --------- | ----------- |
  | Ubuntu-22.04 | Running   | 1           |

**Disabling Hyper-V**

* Hyper-V is a built-in Windows hypervisor. It must be disabled to allow VirtualBox to function as expected.

* Instructions to disable Hyper-V:

  ```
  https://docs.microsoft.com/en-us/troubleshoot/windows-client/application-management/virtualization-apps-not-work-with-hyper-v
  ```

* Additional command to disable Hyper-V:

  ```
  bcdedit /set hypervisorlaunchtype off
  ```

**Troubleshooting**

* WSL Register Distribution Error 0x80370102 in Windows 10/11 (in case of WSL reinstallation):

  ```
  bcdedit /set hypervisorlaunchtype auto
  ```

**Software Installation in WSL Ubuntu**

* Search for "Ubuntu" in the Windows search bar. It will show the installed Ubuntu versions.

* Open the Ubuntu terminal. Sometimes it might take a few minutes to complete the installation, so wait patiently.

* After installing, It will ask for a username and password. Enter your desired username and password, then press Enter. This will create a user and log you into the terminal as the created user. (Do not switch to the root user or create another user to avoid confusion and complexity until the setup is complete.)

* Open VirtualBox in parallel. If it shows any permission error while opening, open it in administrator mode.

* Download and install VirtualBox 7.1.4 using the links below:

  Reference Link: [https://download.virtualbox.org/virtualbox/7.1.4/](https://download.virtualbox.org/virtualbox/7.1.4/)
 
  Direct Download: [https://download.virtualbox.org/virtualbox/7.1.4/VirtualBox-7.1.4-165100-Win.exe](https://download.virtualbox.org/virtualbox/7.1.4/VirtualBox-7.1.4-165100-Win.exe)

* Download the Vagrant's latest version by running the following commands in the Ubuntu terminal:

  ```bash
  wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
  echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
  sudo apt update 
  sudo apt install vagrant=2.4.7-1 -y
  vagrant --version
  vagrant plugin install vagrant-scp
  vagrant plugin install vagrant-disksize
  ```

* Update the hosts file on your host machine so that browser DNS requests resolve to the local VM.

* For Windows: Open the C drive and navigate to `/Windows/System32/drivers/etc/hosts`

* For macOS: Open the default terminal and open the file using:

  ```bash
  nano /etc/hosts
  ```

  Reference: [https://kinsta.com/knowledgebase/edit-mac-hosts-file/](https://kinsta.com/knowledgebase/edit-mac-hosts-file/)

* After opening the file add the below content and save it

  ```
  192.168.56.15 mise.client.id-pal.com websubmission.mise.client.id-pal.com websubmission.tusa.client.id-pal.com tusa.client.id-pal.com
  ```

**Install Ubuntu packages**

* Run the following commands in your Ubuntu terminal:

  ```bash
  sudo apt-get install make keychain unzip -y
  ```

**Install AWS CLI**

* Run the following commands in your Ubuntu terminal:

  ```bash
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  ```

**Configure MFA alias for your bash shell**

* Follow this guide:
  [https://id-pal.monday.com/boards/1735082033/pulses/1738828441](https://id-pal.monday.com/boards/1735082033/pulses/1738828441)

  ```
  mfa <aws_mfa_code_here>
  ```

**Create Projects Folder**

* Create the project folder in your filesystem based on your operating system.

  Windows:

  ```bash
  mkdir /mnt/c/Users/<you>/ID-Pal/
  ```

  macOS:

  ```bash
  mkdir /Users/<username>/<projects_here>/<project_folder>
  ```

**Creating and Adding GitHub Key**

* Run the following command to generate a new SSH key:

  ```bash
  ssh-keygen -t ed25519 -C "your_email@example.com"
  ```

* It will ask for a passphrase. Enter it for security.

* By default, the key will be saved at `/home/<user>/.ssh/id_ed25519`. Leave it as is and press Enter.
* It will generate the private and public keys and save it in the above default path.
* You can check the keys in ```~/.ssh/``` folder
    ```
    ls -al ~/.ssh
    ```
* For the detailed instructions, refer to:

  ```
  https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account
  ```

**Update the variables in user global file**

* Add the following items to your `.profile` or `.bashrc` file located at `$HOME/.bashrc`

  *For WSL only:*

  ```bash
  export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"  export VAGRANT_WSL_WINDOWS_ACCESS_USER_HOME_PATH="/mnt/c/Users/<you>/ID-Pal/dev-box/"
  ```

  *For all platforms:*

  ```bash
  source ~/.aws/alias.sh
  source ~/.aws/token_file

  export JUMPBOX_USER=your_jumpbox_name
  export AWS_PRIVATE_SSH_KEY=~/.ssh/id_rsa

  export GITHUB_TOKEN=your_github_token
  export GITHUB_ID=your_github_username
  export GITHUB_PAT_TOKEN=your_github_fine_grained_pat

  export AWS_USERMFA_ARN=arn:aws:iam::649286310850:mfa/your_aws_username

  # For loading the SSH key
  /usr/bin/keychain -q --nogui $HOME/.ssh/id_rsa
  source $HOME/.keychain/$HOSTNAME-sh

  [[ -r "/usr/local/etc/profile.d/bash_completion.sh" ]] && . "/usr/local/etc/profile.d/bash_completion.sh"
  complete -W "`grep -oE '^[a-zA-Z0-9_.-]+:([^=]|$)' Makefile | sed 's/[^a-zA-Z0-9_.-]*$//'`" make
  ```

* After updating, run the following to apply the variable changes:

  ```bash
  source ~/.bashrc
  ```

**Update the Git credentials**

* Run the following commands to configure Git in Ubuntu:

  ```bash
  git config --global user.name "<your github username>"
  git config --global user.email "<your github email>"
  git config --global credential.helper '!f() { echo username=$GITHUB_ID; echo "password=$GITHUB_PAT_TOKEN"; };f'
  ```

**Cloning the Repo**
* Go to the dev-box path
    ```bash
    cd /mnt/c/Users/<you>/ID-Pal/
    ```

* Clone the repo within the project folder you created earlier:

  ```bash
  git clone https://github.com/ID-Pal/dev-box.git 
  cd dev-box/
  ```


**Build a virtual machine from scratch**

* Authenticate with AWS CLI using MFA code:

  ```bash
  mfa <aws_mfa_code_here>
  ```

* Test if the MFA authentication provides access to AWS:

  ```bash
  make test-aws-cli
  ```

* Run the following command to build the VM:

  ```bash
  make build
  ```

* This process takes approximately 30 to 45 minutes.

* The command performs the following:

  * Creates a VM in VirtualBox
  * Installs packages and dependencies to run the application and Docker containers
  * Clones the necessary ID-Pal private repositories
  * Pulls the PHP 8 and MySQL docker images and creates local containers

* Check the build logs thoroughly for any errors. The build may fail due to network issues or manual interruption.

* Once the build is complete, you will have access to the new VM.

**Getting access to the virtual machine**

* Create an SSH session from WSL:

  ```bash
  make ssh
  ```

**Validation**

1. Check if all containers are in a "healthy" state.

   * To check running containers:

     ```bash
     make show-containers
     ```

   * Check the PHP container logs:

     ```bash
     make logs SERVICE=php8
     ```

2. The local site `https://tusa.client.id-pal.com` should be accessible in your browser.

**Troubleshooting Dev-Box build issues**

* If the dev-box fails at the `make ansible-run` step, follow these steps:

* SSH into the dev-box:

  ```bash
  make ssh
  ```

* If successful, exit the VM and run this from the dev-box repo:

  ```bash
  make ansible-run
  ```

* PHP logs can be found by running `make logs-8` inside the `projects/php8` folder.

* Delete the `laravel_session` cookies (there may be two) before logging into the Web Portal to avoid CSRF Mismatch exceptions.

* To edit code in Visual Studio, use this link:

  ```
  https://idpal-my.sharepoint.com/:w:/g/personal/chayan_singh_id-pal_com/ESNbp1Vp8KFOmw4YIamcVh0BnW_OwqtLIenIbHeEepl35g?e=pesnxo
  ```

**PHPUnit**

* If PHPUnit is not installed during the build process, SSH into the container and install it:

  ```bash
  composer require phpunit/phpunit 6.0.8 --dev
  ```

**Increasing RAM for the Composer command**

* If it fails due to insufficient memory, increase PHP’s `memory_limit` setting in `php.ini` to `-1` (unlimited).

* To find the loaded config file:

  ```bash
  php -i
  ```

* The closest file is `/usr/local/etc/php/conf.d/local.ini`. Set `memory_limit=-1` there and restart the SSH session.

* This allows up to 3GB of memory. To increase it further, edit the `Vagrantfile` and set the desired RAM (default: 3072 MB).

* Restart the DevBox and confirm the new RAM:

  ```bash
  free -h
  ```

* After installation, revert both the PHP `memory_limit` and DevBox RAM settings.

**Version issue**

***Error***

```
The provider 'virtualbox' that was requested to back the machine 'default' is reporting that it isn't usable on this system. The reason is shown below:
Vagrant has detected that you have a version of VirtualBox installed that is not supported by this version of Vagrant. Please install one of the supported versions listed below to use Vagrant:
4.0, 4.1, 4.2, 4.3, 5.0, 5.1, 5.2, 6.0, 6.1, 7.0.
A Vagrant update may also be available that adds support for the version you specified. Please check www.vagrantup.com/downloads.html to download the latest version.
make: *** [Makefile:75: stop] Error 1
```

![](https://github.com/ID-Pal/dev-box/raw/main/structure/vscode_readme_files/Vagrant-Error.png)

**Solution**

* Validate the existing Vagrant and VirtualBox versions on your machine.

  ```bash
  cd /mnt/c/Users/<User>/ID-Pal/dev-box
  vagrant --version
  ```

* If the Vagrant version is not compatible, install a specific version:

  ```bash
  sudo apt install vagrant=<version>
  # Example:
  sudo apt install vagrant=2.4.3-1
  ```

* Remove the existing VM:

  ```bash
  make rm
  ```

* Rebuild the dev-box:

  ```bash
  make build
  ```

* If you're using Vagrant version 2.4.3 or above, upgrade VirtualBox to version 7.1.4.

![](https://github.com/ID-Pal/dev-box/raw/main/structure/vscode_readme_files/Oracle-VirtualBox.png)

***Error***

```
Stderr: VBoxManage.exe: error: Raw-mode is unavailable courtesy of Hyper-V. (VERR_SUPDRV_NO_RAW_MODE_HYPER_V_ROOT).
VBoxManage.exe: error: Details: code VBOX_E_VM_ERROR (0x80bb0003), component ConsoleWrap, interface IConsole.
VBoxManage.exe: error: Context: "LaunchVMProcess(a->session, machine->GetId(), env, externalStartup)" at line 834 of file VBoxManageMisc.cpp
```

![](https://github.com/ID-Pal/dev-box/raw/main/structure/vscode_readme_files/Virtual-Box-stderr.png)

**Solution**

* Uninstall VirtualBox, reinstall it, and reboot your machine.
