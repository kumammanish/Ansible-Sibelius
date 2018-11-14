# Ansible-Sibelius
Popular Configuration Management tools:
Ansible,Chef, Puppet,etc.

Benefits of Ansible
•	Simple: Very easy to install, setup and learn. Written in YAML file which is pretty much like reading English.
•	Agentless: Do not need to install any agents on target nodes.
•	Powerful: It can model any complex IT workflow as it has 1100+ modules
•	Efficient: You can customize modules, using any programming language
•	Secure: Uses SSH for connection
Wondering about YAML language?? Read on to discover

What is YAML?
YAML stands for Yet Another Markup Language. It is a data serialization language just like JSON.
Why YAML, when we already have JSON or XML?
•	YAML files are easy to read and write for humans, similar to English.

 
•	YAML files should end as .yaml or .yml
•	Begins with --- and ends with …
•	# defines comment
YAML is Case and Indentation Sensitive
•	Members of a list should be at the same indentation level starting with a dash(-) and space.
•	Each item in the list is a key: value pair (colon must be followed by a space), called as dictionary.
•	At each level, exactly two spaces are used for indentation. Using tabs is not recommended here.


How Ansible Works?
As already discussed, Ansible is a configuration management tool, based on push-based architecture to automate configuration of your hosts to achieve a desired state.
Following are the components of Ansible architecture
•	Inventory - Defines the list of target hosts
•	Playbook (YAML file) - Defines list of tasks
•	Module - A python code invoked from tasks and executed on hosts
•	Control Machine - Takes playbook and executes each task on particular group of hosts

Playbook
**A Playbook is a file that defines the desired state of your system.
It contains plays, which has a list of tasks to run in sequence against a list of hosts**.
•	A play is set of tasks, grouped together to achieve an objective
•	A task is an instruction you give to Ansible.
•	They are written in YAML format, a data serialization language, that we discussed in previous cards.
Inventory
The Inventory file in Ansible contains the list of all hosts (target systems/servers) that need to be configured. You can also group hosts under different names.
[group A]
Host 1
[group B]
Host 2
Host N

•	Default location of Inventory File: /etc/ansible/hosts
•	You can define an Inventory file statically (.ini file) or dynamically (.json file) to Ansible.
        

Modules Perform the Action
Modules are a piece of code that gets executed when you run playbook. You use them to describe the state you want the host to be in.
Each task in play is made of module and arguments.

•	Also referred as task plugins or library plugins
•	Default location for Ansible modules is /usr/share/ansible
•	Take arguments in key=value format (state=stopped)
•	Returns data in JSON format
•	Modules should be idempotent, meaning they should not do any changes if current state of system matches with the desired final state
•	To access list of all installed modules using command line: ansible-doc -l
•	To see the documentation of particular module using command line: ansible-doc yumwhere yum is the module name
•	You can run modules from the command line or include them in Playbook.
•	Ansible allows you to write your own module (this you will learn later in advanced courses of Ansible).


Ad-hoc Keywords
Before hopping into Ad-Hoc commands, let us first learn Ansible keywords:
•	ansible: This is a tool that allows you to run a single task at a time.
$ ansible <host-pattern> [-m module_name] [-a args] [options]
•	ansible-playbook: This is the tool used to run ansible playbook
$ ansible-playbook <filename.yml> ... [options]

•	ansible-console: This is a REPL using which you can run ad-hoc commands on chosen inventories.
$ ansible-console <host-pattern> [-m module_name] [-a args] [options]
•	ansible-pull: This inverts the default push architecture of Ansible into a pull architecture, which has near-limitless scaling potential.
ansible-pull -U URL [options] [ <filename.yml> ]
•	ansible-doc: Displays data on modules installed in Ansible libraries.
$ ansible-doc [-M module_path] [-l] [-s] [module...]
•	ansible-vault: Using this you can encrypt any structured data file used by Ansible.
$ ansible-vault [create|decrypt|edit|encrypt|rekey] [--help] [options] file_name
•	ansible-galaxy: This is a shared repository for Ansible roles. This ansible-galaxy command can be utilized to manage these roles, or to create a skeleton framework for the roles to be uploaded to Galaxy.
$ ansible-galaxy [delete|import|info|init|install|list|login|remove|search|setup] [--help] [options]
Short Hands In Ansible
•	-a: This tells the arguments to pass to the module
•	-m: Execute the module
•	-b: Use privilege escalation (become)
•	-i: The path to the inventory, which defaults to /etc/ansible/hosts
•	--version: Show program version number
•	--help: Shows help message

APT Module
APT (Advanced Package Tool) is a command-line tool used to easily manage (install, remove, search, etc.) packages on Ubuntu/Debian based Linux systems.
Debian Based OS: Ubuntu, Kali Linux, SteamOS and much more.
You can try the following in Katacoda
$ ansible host01 -i myhosts -m apt -a "name=sudo state=latest"
#This is how you write in Playbook
- name: Upgrade sudo to the latest version
  apt:`
    name: sudo
    state: latest

UM Module
YUM (Yellowdog Updater Modified) is a command-line tool used to easily manage (install, remove, search, etc.) packages on RPM (Red Hat Package Manager) based Linux systems.
Red Hat Based OS: Fedora, Qubes OS, CentOS and much more.
#This is how you write in Playbook
- name: upgrade all packages
  yum:
    name: sudo
    state: latest


shell Module
In shell module, the command name is followed by arguments, which runs on remote hosts through a shell(/bin/sh).
You can use various operations(|,<,> etc) and environment variable(#HOME).
ansible host01 -i myhosts -m shell -a "echo $TERM": This displays the terminal name of host machine
#This is how you write in Playbook
- name: Execute the command in remote shell
  shell: echo $TERM


command Module
In command module, the command name is followed by arguments, which does not run on remote hosts through a shell(/bin/sh).
You cannot use various operations(|,<,> etc) and environment variable(#HOME).
•	To check the list of files or folder in remote host: ansible host01 -i myhosts -m command -a "ls"
•	Make a directory in remote host: ansible host01 -i myhosts -m command -a "mkdir folder1"
•	Check files or folders in remote host: ansible host01 -i myhosts -m command -a "ls"
Now to check files or folders in your terminal use ls and observe the output. As you can see, using command, you can execute tasks on remote host.

Command Everytime
Most of the Ansible modules are idempotent.
But command module does not exhibit this property as this runs every time you run playbook. Hence you will always find the changed status on running the same Playbook again and again.
Consider you wrote a task to copy a file to remote hosts using command module.
Ansible command module will copy the same file the number of times you run the Playbook.
Had this been idempotent; Ansible will not copy from the second time, as the file is already present (current state = desired state).

command can be Idempotent
To overcome this, you can use creates or remove parameter, where you define the filename/pattern.
•	creates: if filename exists, task will not run
•	remove: if filename does not exist, task will not run
#This is how you write in Playbook
- name: copy a file, but do not copy if the file already exists
  command: cp /home/dist/file1.txt /usr/someFolder/ creates=file1.txt
 
Template Module
Ansible template is a file having a defined structure with some variables and expressions, which can be replaced with the corresponding values to generate new configuration file.
•	Template files are written in Jinja2 language (.j2) (Read on to know more about Jinja2)
•	Generally, in Ansible, these files are copied to remote hosts in JSON or YAML format
There are basically two files involved to define templates in Ansible:
•	playbook(YAML file): here you substitute variables in template file with values
•	template(Jinja2 file): here you define the template file in Jinja2 format



 

Environment at a Glance
Control Machine Requirements
•	Linux/Mac OS (Windows not supported)
•	Python (2.6 or later version) should be installed.
•	Install Ansible.
Host Requirements
•	Only Python needs to be installed.
•	Uses SSH to connect to control machine.
•	SFTP is used else scp can be configured.

Install Ansible
Once you are done with Virtual Box and Vagrant setup, you only need to run one last command in your terminal to install Ansible on the control machine.
On Ubuntu/Debian/Linux Mint
$ sudo apt-get install ansible
On RHEL/CentOS/Fedora
$ sudo yum install epel-release

$ sudo yum install ansible
To verify if Ansible is successfully installed or not: ansible --version
You can follow Ansible Official Docs for detailed instructions.



ansible.cfg
You told how your hosts should behave (via Playbook). But how do you tell Ansible (your control machine) should behave?
through ansible.cfg
ansible.cfg is a configuration file that defines how Ansible should behave.
It tells
•	how to establish an ssh connection
•	duration of ssh connection with the host
•	how to run the playbook
•	where to log errors that might occur while playing playbook on hosts etc.
Your ansible.cfg file will have the following settings:
•	[defaults]
•	[privilege_escalation]
•	[paramiko_connection]
•	[ssh_connection]
•	[accelerate]
You will now learn the frequently used settings in upcoming cards. For detailed understanding, you may refer to the Ansible Official Docs.

[defaults]
poll_interval
For asynchronous tasks in Ansible, this tells the frequency of checking the status of task completion. The default value is 15 seconds; which implies, for every 15 sec, it will check if the task is completed.
poll_interval = 15
sudo_user
This is the default user to sudo. If --sudo-user is not specified in an Ansible playbook, the default is the most logical: ‘root’:
sudo_user = root

ask_pass
This tells if Ansible Playbook should ask for a password by default. If SSH key is used for authentication, the default behavior is no.
ask_pass = True
ask_sudo_pass
Just like ask_pass, this asks for sudo password by default while sudoing.
ask_sudo_pass = True
remote_port
If your systems did not define an alternate value in Inventory for SSH port, this sets the default value to 22.
remote_port = 22


[privilege_escalation]
*For some tasks to execute in Ansible, you require administrative access to the system.
The settings under [privilege_escalation] will escalate your privileges.*
become
If set to true or yes, you activate privilege escalation.
Default behavior is no.
become=True
become_method
You can define the method for privilege escalation.
Default is sudo, other options are su, pbrun, pfexec, doas, ksu.
become_method=sudo

become_user
This allows you to become the user over privilege escalation.
Default is root.
become_user=root
become_ask_pass
Asks the password for privilege escalation, the default is False.
become_ask_pass=False












Other Settings
accelerate_timeout
This setting will close the socket connection if no data is received from the client for the defined time duration.
default: accelerate_timeout = 30
record_host_keys
If the setting is True(default value), this will record new hosts on the user's host file, provided host key checking is enabled. Setting it to False, the performance will improve and which is recommended when host key checking is disabled.
record_host_keys=False
pipelining
If enabled, the number of SSH connections required for execution of a module on the remote server is reduced, improving the performance significantly.
By default this is disabled: pipelining = False.
