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

