**Steps for configuring new local dev-box environment**

**System Requirements**

1. Operating System - Windows 11 22H2 | macOS
2. VirtualBox 7.1.4 (It should be a fresh installation. If it already exists, uninstall completely and install freshly)
3. Vagrant 2.4.5+
4. AWS CLI (latest version)
5. GitHub Personal Access Token(PAT - fine grained token)
    
    
**Create the github PAT token**

To create a new PAT token, follow the steps provided here

```bash
[https://id-pal.monday.com/boards/1735082033/pulses/5797018445](https://id-pal.monday.com/boards/1735082033/pulses/5797018445)
```

**Install WSL and Ubuntu**

- Follow the instructions to install the Windows Subsystem and update to WSL2.

    ```
    https://learn.microsoft.com/en-us/windows/wsl/install
    ```

- Once the WSL is installed, it will automatically install Ubuntu under the WSL. But we need to install minimum Ubuntu 22.04 or other latest LTS Ubuntu versions(24.04 LTS) from the Microsoft Store
- To install Ubuntu open the Microsoft Store and type ubuntu in the search bar. It will list out available Ubuntu versions.
- Click on ‘get’ and it will start installing Ubuntu on the local machine
- Once the Ubuntu Linux terminal is installed, open PowerShell in administration mode and run the following command to see that the Linux distribution is properly installed. Take note of the 'Name' and 'Version' of the distribution.

    ```
    wsl --list --verbose
    ```

    Output Example:

    | **NAME** | **STATE** | **VERSION** |
    | --- | --- | --- |
    | Ubuntu | Running | 2 |
    | Ubuntu -22.04 | Running | 2 |
- Run the following command to set your distribution to run as WSL1. WSL must be updated to WSL2, but the distribution should be set to WSL1.

    ```
    wsl --set-version Ubuntu-22.04 1
    ```

    Output Example:

    | **NAME** | **STATE** | **VERSION** |
    | --- | --- | --- |
    | Ubuntu -22.04 | Running | 1 |

**Disabling Hyper-V**

- Hyper-V is a Windows-built-in hypervisor. Hyper-V must be disabled to allow VirtualBox to perform as expected.
- Instructions on how to disable Hyper-V:

    ```
    https://docs.microsoft.com/en-us/troubleshoot/windows-client/application-management/virtualization-apps-not-work-with-hyper-v
    ```

- Additional command to disable Hyper-V

    ```
    bcdedit /set hypervisorlaunchtype off
    ```

**Troubleshooting**

- WSL Register Distribution Error 0x80370102 issue in Windows 10 / 11 in case of reinstallation of the WSL

    ```
    bcdedit /set hypervisorlaunchtype auto
    ```

**Software Installation in WSL Ubuntu**

- Search as ubuntu in the window search bar. It will show the installed ubuntu versions
- Open the Ubuntu terminal. Sometimes it might take some minutes to install. So wait until it install completely.
- After that, it will ask for a username and password. Enter your username and the password and press enter. It will create the user and enter into the terminal as created user(Don’t Switch to root user or create another user hereafter to avoid the confusion and complexity until the setup complete).
- Open the VirtualBox parallelly. If it shows any permission error while opening, open it as administrator mode
- Download and install VirtualBox 7.1.4 using the links below:

    Reference Link: [https://download.virtualbox.org/virtualbox/7.1.4/](https://download.virtualbox.org/virtualbox/7.1.4/)

    Direct Download link: [https://download.virtualbox.org/virtualbox/7.1.4/VirtualBox-7.1.4-165100-Win.exe](https://download.virtualbox.org/virtualbox/7.1.4/VirtualBox-7.1.4-165100-Win.exe)

- Download the Debian package of Vagrant’s latest version by running the following commands in the Ubuntu terminal

    ```
    wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    sudo apt update && sudo apt install vagrant
    vagrant --version
    vagrant plugin install vagrant-scp
    vagrant plugin install vagrant-disksize
    ```

- Update the hosts file for on your host machine so that the web-browser DNS requests will resolve to the local VM
- For Windows: Open the C drive and open the host file from /Windows/System32/drivers/etc/hosts path
- For macos: Open the default terminal and open the file **nano /etc/hosts.** (ref:[https://kinsta.com/knowledgebase/edit-mac-hosts-file/](https://kinsta.com/knowledgebase/edit-mac-hosts-file/))

    ```
    192.168.56.15 mise.client.id-pal.com websubmission.mise.client.id-pal.com websubmission.tusa.client.id-pal.com tusa.client.id-pal.com
    ```

**Install Ubuntu packages**

- Run below mentioned commands on your Ubuntu terminal

    ```
    sudo apt-get install make keychain unzip
    ```

**Install aws-cli**

- un below mentioned commands on your Ubuntu terminal

    ```
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip
    sudo ./aws/install
    ```

**Configure MFA alias for you bash shell.**

- [https://id-pal.monday.com/boards/1735082033/pulses/1738828441](https://id-pal.monday.com/boards/1735082033/pulses/1738828441)

    ```
    mfa <aws_mfa_code_here>
    ```

**Create Projects Folder**

- Create the project folder in your filesystem based on the operating system.

    Windows:

    ```
    mkdir /mnt/c/Users/<you>/ID-Pal/
    ```

    MacOS:

    ```
    mkdir /Users/<username>/<projects_here>/<project_folder>
    ```

**Creating and Adding Github key**

- For detailed instruction and clarification refer the below link

    ```
    https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account
    ```

- Run the below command to generate a new SSH key

    ```bash
    ssh-keygen -t ed25519 -C "your_email@example.com"
    ```

- This will ask for passphrase. Enter the passphrase for the security.

- By default it will save the keys in the /home/<user>/.ssh/id_ed25519  path. Leave it as it is and press enter. Now the keys will be generated with the provided passphrase and save in the mentioned path.


**Update the variables in user global file**

- Add the below items in your .profile or .bashrc file found at "$HOME/bashrc"

    For WSL only

    ```
    export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"
    export VAGRANT_WSL_WINDOWS_ACCESS_USER_HOME_PATH="/mnt/c/Users/<you>/Documents/<project_ folder>/dev-box/"
    ```

    All Platforms

    ```
    source ~/.aws/alias.sh
    source ~/.aws/token_file

    export JUMPBOX_USER=your_jumpbox_name
    export AWS_PRIVATE_SSH_KEY=~/.ssh/id_rsa

    export GITHUB_TOKEN=your_github_token
    export GITHUB_ID=your_github_username
    export GITHUB_PAT_TOKEN=your_github_fine_grained_pat

    export AWS_USERMFA_ARN=arn:aws:iam::649286310850:mfa/your_aws_username

    # For Loading the SSH key
    /usr/bin/keychain -q --nogui $HOME/.ssh/id_rsa
    source $HOME/.keychain/$HOSTNAME-sh

    [[ -r "/usr/local/etc/profile.d/bash_completion.sh" ]] && . "/usr/local/etc/profile.d/bash_completion.sh"
    complete -W "\`grep -oE '^[a-zA-Z0-9_.-]+:([^=]|$)' ?akefile | sed 's/[^a-zA-Z0-9_.-]*$//'\`" make
    ```

- After updating the variables source the profile file to load the new variables into your bash session

    ```
    source ~/.bashrc
    ```

**Update the git credentials**

- Run the below command to add the github authentication in the Ubuntu

    ```
    git config --global user.name "<your github username>"
    git config --global user.email "<your github email>"
    git config --global credential.helper '!f() { echo username=$GITHUB_ID; echo "password=$GITHUB_PAT_TOKEN"; };f'
    ```

**Cloning the Repo**

```
cd /mnt/c/Users/<you>/ID-Pal/ (the path where you created your project folder)
```

- clone the repo within the project folder you created earlier

    ```
    git clone https://github.com/ID-Pal/dev-box.git 
    cd dev-box/
    ```

**Build a virtual machine from scratch**

- Authenticate with AWS CLI using MFA code

    ```
    mfa <aws_mfa_code_here>
    ```

- Test if the MFA authentication provided you access to the AWS

    ```
    make test-aws-cli
    ```

- Run the below command to build the VM

    ```
    make build
    ```

- This will take some amount of time to build(Approximately 30 to 45 minutes).

- This command process the below steps

    - VM in the virtual box
    - Install the packages and dependencies to run the application and Docker container
    - Clone the necessary ID-Pal private repositories
    - Pull the php8, mysql images and create the container from those images

- Check the logs thoroughly if there are any errors occurred during the build. Due to network interruption or manual interruption build might be failed.

- After the build is completed you can able to access the new VM

**Getting access to the virtual machine**

- create a SSH session from WSL

    ```
    make ssh
    ```

**Validation**

1. Check if  all the containers are in "healthy” state
    - To check the running container
    
        ```bash
        make show-containers
        ```
    
    - Check the php container logs using the below command
    
        ```bash
        make logs SERVICE=php8
        ```
    
2. The local site "tusa.client.id-pal.com" can be accessed in the browser

**Troubleshooting Dev-Box**

- If the dev-box fails at make ansible-run step, please follow below mentioned seps
- SSH into the devbox

    ```bash
    make ssh
    ```

- If above command is successful then exit the VM and run the below command from the dev-box repo

    ```bash
    make ansible-run
    ```

- The PHP logs can be found using make `logs-8` inside the projects/php8 folder
- Delete laravel_session cookies (there may be two) before logging into Web Portal to avoid a CSRF Mismatch exception
- Please find the link to edit code in Visual Studio 
    ```
    https://idpal-my.sharepoint.com/:w:/g/personal/chayan_singh_id-pal_com/ESNbp1Vp8KFOmw4YIamcVh0BnW_OwqtLIenIbHeEepl35g?e=pesnxo
    ```

**PHPUnit**

- If phpunit is not installed during build process, `ssh` into container and install it with this command:

    ```
    $ composer require phpunit/phpunit 6.0.8 --dev
    ```

**Increasing RAM for the Composer command**

- If it fails due to insufficient memory, you need to increase PHP's `memory_limit` setting in `php.ini` to `-1`, which means unlimited. You can find out where is the loaded configuration file:

    ```
    $php -i
    ```

- There is no `php.ini` in the container, but the closest one is: `/usr/local/etc/php/conf.d/local.ini`. Set `memory_limit=-1` and restart the `ssh` session. That should provide more memory, up to 3GB as enabled for DevBox. If you need even more RAM, change DevBox's setting in `Vagrantfile` to intended value of megabytes (default is 3072). Restart the DevBox and check if it runs with the new amount of RAM:

    ```
    $ free -h
    ```

- After the phpunit is installed, make sure to revert both PHP `memory_limit` and the DevBox's RAM.

**version issue**

***Error***
```
The provider 'virtualbox' that was requested to back the machine'default' is reporting that it isn't usable on this system. The reason is shown below:Vagrant has detected that you have a version of VirtualBox installed that is not supported by this version of Vagrant. Please install one of the supported versions listed below to use Vagrant:4.0, 4.1, 4.2, 4.3, 5.0, 5.1, 5.2, 6.0, 6.1, 7.0. A Vagrant update may also be available that adds support for the version you specified. Please check [www.vagrantup.com/downloads.html](http://www.vagrantup.com/downloads.html) to download the latest version. make: *** [Makefile:75: stop] Error 1
```
    
    ![](https://github.com/ID-Pal/dev-box/raw/main/structure/vscode_readme_files/Vagrant-Error.png)
    

**Solution**

- We need validate the existing `vagrant version` and `virtual box version` in the local machine. Command to validate the Vagrant Version.

    ```
    $ cd /mnt/c/Users/<User>/ID-Pal/dev-box
    ```

    ```
    $ vagrant --version
    ```

- If vagrant version is not compatible, We need to upgrade the vagrant to a specific version that is compatible using the below command.

    ```
    $  sudo apt install vagrant=<version>
    (eg)
    $ sudo apt install vagrant=2.4.3-1
    ```

- Execute the below command to remove the existing VM

    ```bash
    $ make rm
    ```

- Re-build the dev-box setup by running

    ```
    $ make build
    ```

- If you're using Vagrant version `2.4.3` and above, you need to upgrade VirtualBox to version `7.1.4` for compatibility.
    
    ![](https://github.com/ID-Pal/dev-box/raw/main/structure/vscode_readme_files/Oracle-VirtualBox.png)

***Error***
```   
Stderr: VBoxManage.exe: error: Raw-mode is unavailable courtesy of Hyper-V. (VERR_SUPDRV_NO_RAW_MODE_HYPER_V_ROOT). VBoxManage.exe: error: Details: code VBOX_E_VM_ERROR (0x80bb0003), component ConsoleWrap, interface IConsole.VBoxManage.exe: error: Context: "LaunchVMProcess(a->session, machine->GetId(), env, externalStartup)" at line 834 of file VBoxManageMisc.cpp
```
    
    ![](https://github.com/ID-Pal/dev-box/raw/main/structure/vscode_readme_files/Virtual-Box-stderr.png)
    

**Solution** 
- Uninstall VirtualBox, then reinstall it. After the installation is complete, make sure to reboot your machine.
