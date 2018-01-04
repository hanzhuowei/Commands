#set default python to python3
I would still recommend using #!/usr/bin/python3 (or #!/usr/bin/env python3) in scripts for simpler cross-compatibility. 
A simple safe way would be to use an alias. Place this into ~/.bashrc or ~/.bash_aliases file:

alias python=python3

After adding the above in the file. run the below command

source ~/.bash_aliases or source ~/.bashrc


#update to 3.6
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
