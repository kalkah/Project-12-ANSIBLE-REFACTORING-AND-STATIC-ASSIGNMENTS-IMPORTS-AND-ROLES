# Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES

## ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

In this project, we will continue working with **`ansible-config-mgt`** repository and make some improvements on our code. We will refactor our Ansible code, create assignments and learn how to use the import functionality. Imports enables the ability to effectively re-use previously created playbooks in a new playbook. In essence, it allows us to organise our tasks and reuse them when necessary. For better undestanding of Ansible artifacts reuse [read this article](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse.html) 

### <br>Introduction to Code Refactoring<br/>
 
The goal of this project is to demonstrate how Ansible refactoring works and its usefulness. [Refactoring](https://en.wikipedia.org/wiki/Code_refactoring) is a general term in computer programming. It means making changes to the source code without changing the expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity and add proper comments without affecting the logic. In this project, we will be making some slight changes to the code but the overall state of the infrastructure shall remain the same. This project shall consist of three parts:

**1.** Refactor Ansible Code.

**2.** Configure UAT Webservers with Roles.

**3.** Reference Webserver Role.

### <br>Refactor Ansible Code <br/>

In the first part of our project, we shall refactor Ansible code by importing other playbooks into **`site.yml`**. However, before we begin, we need to make some changes to our Jenkins Job. 

#### <br>Step 1: Jenkins Job Enhancement<br/>

With the way our Jekins job is currently configured, every new change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins serves with each subsequent change. To enhance it, we shall be introducing a new Jenkins project/job and we will require **`Copy Artifact`** plugin.

**i.** In our **`Jenkins-Ansible`** server, we use the comand below to create a new directory called **`ansible-config-artifact`** – which we will be using to store all artifacts after each build.

**` $ sudo mkdir /home/ubuntu/ansible-config-artifact`**

**ii.** Then with the following command, we change permissions for this directory, so that Jenkins would be able to save files there:

**` $ chmod -R 0777 /home/ubuntu/ansible-config-artifact`**

<img width="479" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/9fde77a4-748b-4fb9-9a92-ca99d17099ea">

**iii.** Next, we will need to install the **`copy artifact`** plugin without restarting Jenkins:

+ From the main Jenkins Environment, we click on **"Manage Jenkins"**. On the System Configuration page, we click on **"Plugins"**. On the Plugins page, we click on Available plugins, then we type **`copy artifact`** in the search box.

<img width="953" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/c9193c3d-4c0e-4748-8a1c-03188ab5a467">

+ From here, we can see the **`copy artifact`** plugin we wish to install, so we check the **"Install"** checkbox beside it and we click on the **"Install"** button. After doing this we will be able to see the download progress page.
  
<img width="958" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/d5204d7b-9200-45c5-850e-707d20fc61ca">

**iv.** Next, we create a new Freestyle project (as we did in [Project 11](https://github.com/kalkah/Project-11-Ansible-Automate)) and we name it **`save_artifacts`**:

+ From the Jenkins web console, we click on **"New item"**

+ In the next page under **"Enter an item name"** we type in **`save_artifacts`**, then we select **"Freestyle project"** and we click on **"Ok"** at the bottom of the page.

**v.** This project will be triggered by the completion of our existing **`ansible`** project. So therefore we configure it accordingly:

+ In the Jenkins configuration page, under **"General"** we click on check box for **"Discard old builds"** and under **"Max # of builds to keep"** we enter our desired number (to save space on the server).
+ 
<img width="943" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/ec8c046b-e5d3-40aa-b45c-a0602faeaa68">

+ Next under **"Build Triggers"**, we click on the check box beside **"Build after other projects are built"** and under the **"Projects to watch"** dialogue box we type in **`ansible`**.
  
<img width="947" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/511d2de4-750b-44bc-b738-c5b9136a3a0e">

**vi.** The main idea of the **`save_artifacts`** project is to save artifacts into the **`/home/ubuntu/ansible-config-artifact`** directory. To achieve this, we proceed as follows:

+ Under **"Build Steps"** we click on the **"Add build step"** drop down button and we select **"Copy artifacts from another project"**.
+ Under **"Project name"**, we specify **`ansible`** as the source project, under **"Artifacts to copy"**, we input __**__ to copy all artifacts and then we put in  **`/home/ubuntu/ansible-config-artifact`** as the **"Target directory"**.
+ Then we click on **"Apply"** and **"Save"** at the bottom of the page.
+ 
<img width="946" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/062b894c-5715-4a09-b2d8-3a2201d2056f">

**vii.** The next thing we do is to test our set up by making some changes in the **README.MD** file inside our **`ansible-config-mgt`** repository (right inside the **`main`** branch). If both Jenkins jobs have completed one after another – we shall see our files inside the **`/home/ubuntu/ansible-config-artifact`** directory and it will be updated with every commit to our **`main`** branch.

I got the error below when I ran a test build.

<img width="928" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/58f9e3c8-11ab-4913-8caf-d02bd9364a4f">

The error was fixed by runing these two commands to change the permission on the parent directory (/home/Ubuntu): `sudo chown -R jenkins:jenkins /home/ubuntu`  `sudo systemctl restart jenkins`

As seen in the  image below, the **`save_artifacts`** job was successfully triggered and completed.

<img width="600" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/d232165c-1b5b-494a-b165-69bb3bf39221">

 To confirm that our files are inside the **`/home/ubuntu/ansible-config-artifact`** directory, we execute the following command: **` $ sudo ls /home/ubuntu/ansible-config-artifact`**
 
<img width="362" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/539ec592-ddbd-4fad-9ccd-de844772551d">

#### <br>Step 2: Refactor Ansible code by importing other playbooks into `site.yml`<br/>

**i.** Before starting to refactor the codes, we need to pull down the latest code from the **`main`** branch, and create a new branch, which we will name **`refactor`**:

+ From the VS code GUI in the **`main`** branch of our ansible-config-mgt repository, we click on the **"Views and More Actions"** button then we select **"pull"** to pull down the latest code from the **`main`** branch.
+ From the VS Code environment we go to the bottom of the page and we click on **`main`**. Afterwards, we enter the new branch name **`refactor`** and we press **Enter**
  
<img width="956" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/df46dbb5-f3c0-4af1-834b-6d14bfa1903e">

Then we subsequently click on the **"Publish Branch"** button.

**ii.** Next, we enter the command below to checkout the newly created branch **`refactor`** to our local machine and start refactoring our code:   

**`$ git checkout refactor`**

<img width="365" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/6f91c95f-94ac-477e-9a9b-9e24f0c1990f">

**iii.** Then within the playbooks folder, we create a new file and name it **`site.yml`** – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, **`site.yml`** will become a parent to all other playbooks that will be developed. This is including **`common.yml`** that we created previously.

`$ cd playbooks/`    `$ touch site.yml`

<img width="404" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/59f90d97-50e1-4760-b741-389d7d39a478">

iv. The next thing we do is to create a new folder in the root of the repository and name it `static-assignments`. This is where all other children playbooks will be stored. This is merely for easy organization of our work and it is therefore not an Ansible specific concept as we can always choose to organise our work in different ways.  '$ mkdir static-assignments'

<img width="354" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/207b8eb3-2698-4c62-ac52-5b4de8bc0196">

**v.** Next we move **`common.yml`** file into the newly created **`static-assignments`** folder:  **` $ mv playbooks/common.yml static-assignments/`**

<img width="446" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/09f912e8-3465-4c44-bc5f-a3f7c4a20814">

**vi.** Then from inside the **`site.yml`** file, we import the **`common.yml`** playbook.

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```

**vii.** At this point, our folder structure is as shown in the image below:

<img width="700" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/27e9d3a7-38d7-4b38-b292-fa5f88d93760">

#### <br>Step 3: Run `ansible-playbook` command against the `dev` Environment<br/>

Since we need to apply some tasks to our **`dev`** servers and **`wireshark`** is already installed – we proceed to create another playbook under **`static-assignments`** and name it **`common-del.yml`**. However, in this playbook, we configure the deletion of wireshark utility.

**i.** After moving into the **`static-assignments`** directory, we create a new playbook with the following command: **`$ touch common-del.yml`**

![common-del-yml](https://github.com/QBDev0ps/DevOps-Cloud-projects/assets/140855364/d122b8c2-b15c-4830-9cd7-9ae87f73bfb7)

**ii.** Next, we open up the newly created file in VS Code and we paste in the following configuration:

```
---
- name: update web and nfs servers
  hosts: webservers, nfs
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB and db servers
  hosts: lb, db
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```

<img width="899" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/1e47f7c6-4157-4a28-afb9-d1afb333e20f">

**iii.** After completing our configuration, we update **`site.yml`** with **`- import_playbook: ../static-assignments/common-del.yml`** instead of **`common.yml`** as shown below:

```
---
- hosts: all
- import_playbook: ../static-assignments/common-del.yml
```

<img width="707" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/d005bef2-d4af-4270-81e5-f21fa40b0988">

**iv.** Then we proceed to run the playbook against the **`dev.yml`** servers in our inventory file:

`$ cd /home/ubuntu/ansible-config-mgt/`  `$ ansible-playbook -i inventory/dev.yml playbooks/site.yml`
`
<img width="613" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/c9d49b8b-ac21-4170-8b28-b529cf908b8e">
<img width="563" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/d246f3d3-99bf-4672-9471-0a7e0204708a">
<img width="605" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/1adf1a9c-15d4-4f11-9362-37769af729b5">
<img width="616" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/e5687e3f-0bb1-41cc-8d45-6f6988768940">
<img width="674" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/435d0205-bb88-41b0-a2e0-ad467a422326">
<img width="679" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/104e2052-224c-4575-b188-bf8df686034f">

**V** we emsure that wireshark is deleted on all the servers by running : `which wireshark`  `wireshark --version`

<img width="664" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/a5283068-e459-4529-86cf-1da64e84661e">
<img width="451" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/4b1ce76f-31e6-4977-862a-3b36f2a50312">

### <br>Configure UAT Webservers with Roles <br/>

Writing tasks to configure Web Servers in the same playbook we used earlier would make our configuration cumbersome, so instead we decide to make use of a dedicated role which ensures that our configuration would be reusable.

1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly – Web1-UAT and Web2-UAT
<img width="757" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/5784da4f-55bd-4d11-a30d-647902defe1f">

2. To create a role, you must create a directory called roles/, relative to the playbook file or in `/etc/ansible/ directory`.

There are two ways how you can create this folder structure:

i. Use an Ansible utility called ansible-galaxy inside ansible-conf-mangt-P11/roles directory (you need to create roles directory upfront)

`mkdir roles`  `cd roles`  `ansible-galaxy init webserver`

ii. Create the directory/files structure manually

To create this folder structure, we decide to use the `ansible-galaxy utility` inside the `ansible-config-mgt/roles` directory.

<img width="494" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/9b44e513-5175-4624-b780-37291218ba7a">

 At this point, the folder structure looks exactly as shown in the image below.

![image](https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/9f87caa1-56a3-456c-ad44-185d5d1cd704)

 Next, we remove the unnecessary folders tests and vars and their contents. `$ rm -r tests`  `$ rm -r vars`

<img width="452" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/1471feef-3bd3-4b57-a735-6abb77e09a37">

We do not have the templates folder in our directory structure so we manually create it:  `$ mkdir templates`

<img width="470" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/b2f41725-9915-4549-ac6b-e464a17e456a">

After cleaning up our directory, the roles folder structure looks exactly as shown below:

![image](https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/d5966d68-45a4-4bd2-a2da-1c700cb8fe3c)

#### <br>Step 3: Update Configuration for UAT Webservers<br/>

**i.** We proceed to update the inventory **`ansible-config/inventory/uat.yml`** file with the Private IP addresses of the 2 UAT Webservers using the following syntax:

```
[uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user=ec2-user
<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user=ec2-user
```

<img width="275" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/19d95cec-0cf9-4b0c-9b5a-90e64f6a6693">

**ii.** Next, we navigate with the VS Code file explorer to the **`/etc/ansible/ansible.cfg`** file and uncomment the **`roles_path`** string and provide a full path to our roles directory **`roles_path = /home/ubuntu/ansible-config/roles`**, to enable Ansible know where to find configured roles.

<img width="422" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/2741c3a8-974b-42e7-b52f-324afddd8832">

**iii.** The next step is to start adding some logic to the webserver role. We move into the **`tasks`** directory, and within the **`main.yml`** file, we proceed to write configuration tasks to do the following:

+ Install and configure Apache (**`httpd`** service)

+ Clone Tooling website from GitHub **`https://github.com/<your-name>/tooling.git`**.
  
+ Ensure the tooling website code is deployed to **`/var/www/html`** on each of 2 UAT Web servers.
  
+ Make sure **`httpd`** service is started.

**iv.** After completing the configuration, our **`main.yml` file consists of the following tasks:

```
---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/kalkah/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```

<img width="448" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/65f7e8b2-e229-4dcb-b7b9-fca5a41d25a5">

### <br>Reference Webserver Role <br/>

#### <br>Step 1: Reference Web Server Role in `static-assignments` <br/>

**i.** We start by navigating to the **`static-assignments`** folder, and within this directory, we execute the command below to create a new assignment for **uat-webservers** which we name as **`uat-webservers.yml`**. 

**`$ touch uat-webservers.yml`**

**ii.** The next step is to open up the **`uat-webservers.yml`** file via the VS Code file explorer and then we paste in the following configuration to reference the webserver role:

```
---
- hosts: uat-webservers
  roles:
     - webserver
```

<img width="254" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/a8c198df-321e-4be9-b948-4384f5135338">

**iii.** Considering that the entry point to our ansible configuration is the **`site.yml`** file, we therefore need to reference our **`uat-webservers.yml`** role inside **`site.yml`**. So, we navigate via the VS Code file explorer and put the following configuration inside **`site.yml`**:

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml
```

<img width="404" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/02a7e037-ccd6-4bc3-a9ec-0720d070e649">

#### <br>Step 2: Commit and Test <br/>

In this step, we shall commit our changes, create a Pull Request and then merge the changes in the refactor branch to the main branch. Afterwards we will make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to our Jenkins-Ansible server into **`/home/ubuntu/ansible-config/`** directory.

**i.** Using the following set of commands, we add all untracked files to the staging area and then we commit the changes:

`$ git add .`  `$ git commit -m "saved all updates to refactor branch"`

<img width="440" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/6b285518-52d2-4e3e-9d85-50c28a586180">
<img width="605" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/27367c44-86fb-4546-b8c2-adc8cb39ea03">

**ii.** Next we push the refactor branch to our github repository using the following command:

**`$ git push --set-upstream origin refactor`**


**iii.** The next thing we do is to create a **Pull Request** in GitHub by following [these steps:](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request) 

**viii.** The next step is to connect to our **`Jenkins-Ansible`** server via **`ssh-agent`** as we did in Project 11 and then with the commands below, we run the playbook against our **`uat inventory`**.

`$ cd /home/ubuntu/ansible-config-mgt`  `$ ansible-playbook -i /inventory/uat.yml playbooks/site.yml`
```

#### BLOCKER❗
<img width="657" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/4dae400e-3445-4db4-b7d0-b928d9842119">

+ We got the error above when we ran the playbook, but after we examined the error message and did some investigations we realised it was a permision issue. Our current user did not have the adequate rights to **`/inventory/uat.yml`**. So to fix this, we executed the following command:

**`sudo chmod 744 inventory/uat.yml`**

+ And then we ran our playbook again:

**`$ ansible-playbook -i inventory/uat.yml playbooks/site.yml`**

<img width="648" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/38f186a2-d714-4011-b9e1-db6a2c2af6b5">
<img width="669" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/5366b7b4-166f-468c-ab79-2a2f116c9ca9">
<img width="667" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/38313b6c-0cbf-429c-bd24-f17d6726fa50">
<img width="655" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/7f9df376-75b7-4e5b-b9cf-0652263be682">

 To confirm our playbook executed the tasks properly and complete the project, we go to our browser and try to reach the UAT web servers using the syntax below:

`http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php`

<img width="922" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/33c237a5-d459-4efc-a046-a7064364cbd6">

<img width="955" alt="image" src="https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/fed7e763-8584-4856-85b6-3dbd96d1e100">

### <br>Conclusion<br/>

The web browser images above show that we have been able to successfully deploy and configure two UAT Web Servers using Ansible **`imports`** and **`roles`** and our setup looks somewhat like what we have in the image below.

![final architecture]![image](https://github.com/kalkah/Project-12-ANSIBLE-REFACTORING-AND-STATIC-ASSIGNMENTS-IMPORTS-AND-ROLES/assets/95209274/feba4b42-3407-49c4-964c-7cc3aa6f537d)


The goal of this project was to demonstrate how Ansible refactoring works to  enhance code readability, increase maintainability and extensibility. We began the project by enhancing our existing Jenkins Job **`ansible`** with the introduction of a new Jenkins project/job **`save_artifacts`** which required the use of the **`Copy Artifact`** plugin. After this, we pulled down the latest code from the **`main`** branch, and create a new branch **`refactor`** where we began making changes and refactoring ansible code. Essentially, we created a new file **`site.yml`** to not only be seen as a parent to all other playbooks, but also used as an entry point into the entire infrastructure configuration and we included and referenced our other children playbooks from here. To ensure our work stays well organized, we also created a folder **`static-assignments`** in the root of our repository to be used as storage for all our children playbooks. 

The next phase of the project involved using **`roles`** to configure two UAT web servers. We launched 2 fresh Red Hat Enterprise Linux 8, EC2 instances to be used as the **`UAT`** servers, and then in our **`Jenkins-Ansible`** server, we created the **`roles/`** directory and used the **`ansible-galaxy`** utility to generate the appropriate folder structure. The next step was to update the relevant inventory file with the Private IP addresses of the 2 UAT Webservers. Then we attempted to update the ansible configuration file to enable Ansible know where to find configured roles but we ran into a **BLOCKER** here. The **`ansible.cfg`** file was absent from our setup so we had to manually generate it and make the necessary configuration changes.

Afterwards, we started adding some logic to the webserver role and we wrote configuration tasks to install and configure Apache (**`httpd`** service), clone Tooling website from GitHub **`https://github.com/<your-name>/tooling.git`**, ensure the tooling website code is deployed to **`/var/www/html`** on each of 2 UAT Web servers and make sure **`httpd`** service is started. Then we referenced our Web Server Role in the **`static-assignments`** folder. Next, we committed our changes, created a Pull Request and then merged the changes from the refactor branch to the main branch. Afterwards we made sure sure webhook triggered two consequent Jenkins jobs that ran successfully and copied all the files to our Jenkins-Ansible server into **`/home/ubuntu/ansible-config/`** directory.The next step was to connect to our **`Jenkins-Ansible`** server via **`ssh-agent`** as we did in [Project 11](https://github.com/kalkah/Project-11-Ansible-Automate) and then run the playbook against our UAT inventory file where we configured the Private IP addresses of the 2 UAT Webservers. We ran into another blocker here as ansible was unable to gain access to our inventory directory but we were able to rectify this by fixing the permissions of the directory. From here onwards, our playbook ran successfully and via our browser we were able to access the tooling website deployed on our two UAT servers. Thank you.
