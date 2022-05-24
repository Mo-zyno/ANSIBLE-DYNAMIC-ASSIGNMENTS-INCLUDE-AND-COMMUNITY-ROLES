# ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES


In this project we will introduce dynamic assignments by using include module.

Now you may be wondering, what is the difference between static and dynamic assignments?

Well, from the previous Project, you can already tell that static assignments use import Ansible module. The module that enables dynamic assignments is include.

Hence,

import = Static
include = Dynamic


When the import module is used, all statements are pre-processed at the time playbooks are parsed. Meaning, when you execute site.yml playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is static.

On the other hand, when include module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the statements encountered during execution will be used.

Take note that in most cases it is recommended to use static assignments for playbooks, because it is more reliable. With dynamic ones, it is hard to debug playbook problems due to its dynamic nature. However, you can use dynamic assignments for environment specific variables as we will be introducing in this project.


## INTRODUCING DYNAMIC ASSIGNMENT INTO OUR STRUCTURE

### Introducing Dynamic Assignment Into Our structure
In your https://github.com/<your-name>/ansible-config-mgt GitHub repository start a new branch and call it dynamic-assignments.

Create a new folder, name it dynamic-assignments. Then inside this folder, create a new file and name it env-vars.yml. We will instruct site.yml to include this playbook later. For now, let us keep building up the structure.

Your GitHub shall have following structure by now.
  
  
  ![1 dynamic assignments folder](https://user-images.githubusercontent.com/97651517/169938352-f35f3eeb-8be1-4f9a-af6f-c6b7c58cc34d.png)

  

  
Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder env-vars, then for each environment, create new YAML files which we will use to set variables.

Your layout should now look like this.
  
 ![2  env folder](https://user-images.githubusercontent.com/97651517/169938592-0e8632a3-1e66-4c03-a446-8d52eedffcfe.png)
  
  
  Now paste the instruction below into the env-vars.yml file.
  
  
  ![4  code ymlfile](https://user-images.githubusercontent.com/97651517/169938764-3a0d28e4-248e-4613-9b74-8e52d9dc17da.png)
  
  
  Now set necessary permission to each file
  
  ![3  permission file](https://user-images.githubusercontent.com/97651517/169938906-fdbdd7b7-eba3-470b-8aad-b0ff247f2888.png)
  
  
  Notice 3 things to notice here:

We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:
-include_role
-include_tasks
-include_vars

  In the same version, variants of import were also introduces, such as:

-import_role
-import_tasks

  We made use of a special variables { playbook_dir } and { inventory_file }. { playbook_dir } will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. { inventory_file } on the other hand will dynamically resolve to the name of the inventory file being used, then append .yml so that it picks up the required file within the env-vars folder.
We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.
  
  
  ## UPDATE SITE.YML WITH DYNAMIC ASSIGNMENTS

Update site.yml with dynamic assignments
Update site.yml file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats)

site.yml should now look like this.
  
![site yml](https://user-images.githubusercontent.com/97651517/169940115-1dfdf916-5ddf-44be-bff5-ca731eac3493.png)
  
  
Community Roles
Now it is time to create a role for MySQL database – it should install the MySQL package, create a database and configure users. But why should we re-invent the wheel? There are tons of roles that have already been developed by other open source engineers out there. These roles are actually production ready, and dynamic to accomodate most of Linux flavours. With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.
  
  
### Download Mysql Ansible Role
You can browse available community roles here

We will be using a MySQL role developed by geerlingguy.
  
 ![5 geerlyinguy](https://user-images.githubusercontent.com/97651517/169940321-79feebbf-9fbf-4fb5-9004-731e21aa0367.png)


 ![6 install mysql](https://user-images.githubusercontent.com/97651517/169940436-5a737d45-eb3c-4f71-a6f9-3c085a245ac1.png)
  
On Jenkins-Ansible server make sure that git is installed with git --version, then go to ‘ansible-config-mgt’ directory and run
  

![7  checking git version](https://user-images.githubusercontent.com/97651517/169940673-cc4516a1-6147-4e73-a467-61a722cf5a3c.png)

  
Inside roles directory create your new MySQL role with ansible-galaxy install geerlingguy.mysql and rename the folder to mysql
  

  `mv geerlingguy.mysql/ mysql`
  

![8  change name](https://user-images.githubusercontent.com/97651517/169940950-31cbf578-1252-4526-9660-cecd46794d59.png)
  
`git add .`
`git commit -m "Commit new role files into GitHub"`
`git push --set-upstream origin roles-feature`

 Now, if you are satisfied with your codes, you can create a Pull Request and merge it to main branch on GitHub.
  
  
## Load Balancer roles

  We want to be able to choose which Load Balancer to use, Nginx or Apache, so we need to have two roles respectively:

-Nginx
-Apache

  With your experience on Ansible so far you can:
  
  
Decide if you want to develop your own roles, or find available ones from the community
Update both static-assignment and site.yml files to refer the roles
  
  
Important Hints:

- Since you cannot use both Nginx and Apache load balancer, you need to add a condition to enable either one – this is where you can make use of variables.

- Declare a variable in defaults/main.yml file inside the Nginx and Apache roles. Name each variables enable_nginx_lb and enable_apache_lb respectively.

- Set both values to false like this enable_nginx_lb: false and enable_apache_lb: false.

- Declare another variable in both roles load_balancer_is_required and set its value to false as well

- Update both assignment and site.yml files respectively

loadbalancers.yml file
  
  
![10 install nginx](https://user-images.githubusercontent.com/97651517/169941878-e69bdcfb-eb9f-4d52-b594-362d646885f4.png)

  
![11 apache](https://user-images.githubusercontent.com/97651517/169941890-9c56ac24-9304-4178-bff6-eca893cf24cb.png)

![12 change name nginx and apache](https://user-images.githubusercontent.com/97651517/169941903-0e255d5c-8cb0-483c-aeb8-cb38a5f1953b.png)
  
 
  ### Configure your main.yml file
  

  ![9 config](https://user-images.githubusercontent.com/97651517/169942656-d3fdfbcc-a95b-4f0c-89cb-3dd0650866f0.png)

 
  -Continue configuration 
  
  
  ![13 upstream changes](https://user-images.githubusercontent.com/97651517/169943245-6c900647-55ab-4e43-8109-4d327343d4dd.png)
  
  
  ![14 proxy chnage](https://user-images.githubusercontent.com/97651517/169943396-5dafccbc-9124-4973-8a05-301f284f3a4f.png)

  
  Move to task/main.yml 
  
  
  ![15 task file](https://user-images.githubusercontent.com/97651517/169943582-068f0e46-b556-4228-a7b7-40be849f40f0.png)
  
  
  Configure Apache/main.yml file:
  
  
  ![20 corrected apache file](https://user-images.githubusercontent.com/97651517/169944438-dedae898-f5c0-4553-b7fe-74b9414577a5.png)
  
  
  Configure nginx/default/main.yml file
  
  
  ![config nginx maim yml](https://user-images.githubusercontent.com/97651517/169945416-f63a5ff3-2804-4751-b20c-a86f46b668b2.png)

  
  Update  IP address of servers
  
  
  ![ip address of servers](https://user-images.githubusercontent.com/97651517/169945803-9a138ce7-03c9-4821-969d-e166dd6d88c0.png)

  
  Create a Loadbalancer yml file
  
  
  ![22 lb yml](https://user-images.githubusercontent.com/97651517/169946601-3a6e1f89-83f2-469d-abcb-f129a5150a66.png)

  
  Create a Database yml file also
  
  
  ![21  db yml](https://user-images.githubusercontent.com/97651517/169946779-1ad7e2e5-b7a9-4a8f-aec3-5952a2fca344.png)
  
  
  Update site.yml file also:
  
  
  ![new site yml](https://user-images.githubusercontent.com/97651517/169948609-8c7410a3-8453-4f78-aa05-4e1b8aa3aecd.png)

  
  Configure the UAT.yml file
  
  
  ![23  uat updated](https://user-images.githubusercontent.com/97651517/169948870-44190a7a-5201-46b5-a194-afea114cb079.png)

  
  Configure the nginx main.yml file
  
  
  ![config nginx maim yml](https://user-images.githubusercontent.com/97651517/169949403-21b93bd6-634f-4dee-a74a-bebda64db1e3.png)

  
 Now you can make use of env-vars\uat.yml file to define which loadbalancer to use in UAT environment by setting respective environmental variable to true.

You will activate load balancer, and enable nginx by setting these in the respective environment’s env-vars file.

`enable_nginx_lb: true` 
  
`load_balancer_is_required: true`
  
  
![23  uat updated](https://user-images.githubusercontent.com/97651517/169950163-0717d496-191b-4cca-a984-9ec477ef2d88.png)
  
  
The same must work with apache LB, so you can switch it by setting respective environmental variable to true and other to false.

To test this, you can update inventory for each environment and run Ansible against each environment.
  
  
  
  
  
  

