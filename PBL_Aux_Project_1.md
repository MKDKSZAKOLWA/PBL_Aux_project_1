# (STEP 11) AUX PROJECT 1: SHELL SCRIPTING


*It is time to start getting a feel of how to automate some work using Shell Scripts.*

*In this project, we need to onboard 20 new Linux users onto a server. Create a shell script that reads a csv file that contains the first name of the users to be onboarded*


## Create an EC2 instance in Ubuntu.
* Follow the previous steps to create an EC2 instance.
* Create a security group like the one in Project 1 or just use the same security group used for Project 1.



  ![Aux Project 1 Security Group](./Images/Aux%20Project%201%20Security%20Group.PNG)


We will need to prepare and use the script below for our project.


```
# Shell Scripting.
# This script will read a CSV file that contains 20 new Linux users.
# This script will create  each user on the server and add to an existing group called 'Developers'.
# This script will first check for the existance of the user on the system, before it will attempt to create.
# The user that is being created  must also have  a default home folder.
# Each user should have a .ssh folder within its HOME folder. if it does not exist, then it will be created.
# For each user's SSH configuration we will create an authorized_key file and add the below public key.


#!/bin/bash
userfile=$(cat names.csv)
PASSWORD=password

# To ensure the user running this script has sudo privilege
    if [ $(id -u) -eq 0 ]; then

# Reading the CSV file
	for user in $userfile;
	do
            echo $user
        if id "$user" &>/dev/null
        then
            echo "User Exist"
        else

# This will create a new user
        useradd -m -d /home/$user -s /bin/bash -g developers $user
        echo "New User Created"
        echo


# This will create a ssh folder in the user home folder
        su - -c "mkdir ~/.ssh" $user
        echo ".ssh directory created for new user"
        echo

# We need to set the user permission for the ssh dir
        su - -c "chmod 700 ~/.ssh" $user
        echo "user permission for .ssh directory set"
        echo

# This will create an authorized-key file
        su - -c "touch ~/.ssh/authorized_keys" $user
        echo "Authorized Key File Created"
        echo

# We need to set permission for the key file
        su - -c "chmod 600 ~/.ssh/authorized_keys" $user
        echo "user permission for the Authorized Key File set"
        echo

# We need to create and set public key for users in the server
        cp -R "/root/onboard/id_rsa.pub" "/home/$user/.ssh/authorized_keys"
        echo "Copyied the Public Key to New User Account on the server"
        echo
        echo

        echo "USER CREATED"

# Generate a password.
sudo echo -e "$PASSWORD\n$PASSWORD" | sudo passwd "$user" 
sudo passwd -x 5 $user
            fi
        done
    else
    echo "Only Admin Can Onboard A User"
    fi
```



## Explanation of the script in detail

* On the first and second line of the script, we are declaring two variables.
    * userfile 
    * PASSWORD
* The userfile is taking its input from the command line. The command cat will spit the contents of name.CSV on the terminal. The content will be stored intp the userfile.

* We are setting the value for PASSWORD to be password.

* The if statement is checking if the user that is running the script is equivalent to 0. The id of root is zero. This will ensure that the user running the script has sudo prividlege.

* The for loop will help with going through each content of the name.CSV output until it will exhaust what the ask is.

* For everythong that is found in userfile the users needs to do (echo the user).

* Check the id of the user that is being echoed exists.

* else  is used if the user does not exist then create the user.

* `useradd -m -d /home/$user -S /bin/bash -g developers $user` is used to create and add a user in a folder in its own directory and add to a group called developers.

* After the user is created added, it will create the .ssh folder.

* We  then set permissions for the ssh directory to be read, write and executable for the user only.

* Create authorized keys .

* Set permissions for the authorized key file.

* Create public key file.

* Set password for the user to *password*. The user will be forced to change his password after it has logged in 5 times.

* *fi* is used to close the if statement. Also close the for loop with *done*

* If the user is not equal to zero (root user) then it will echo "Only Admin Can Onboard A User". Then close the if statement with *fi*.



Before you connect to the EC2 instance, we will use the scp command to copy the script from the local  machine to the instance. Perform the steps below.


* Go to the terminal.
* Run `cd downloads`.

* ![Downloads Directory](./Images/Downloads%20Directory.PNG)

* The perm key that we used to connect to the EC2 is in downloads. The perm that we used is *DevOps_Projects*.

* Open gitbash.
* Execute `cd downloads`
* Run the command below to create.sh in the fownloads folder.
`vi onboard.sh`
* The blank file will be opened in gitbash.
* ![Blank Onboard.sh](./Images/Blank%20onboard.sh)

* Insert by clicking i and then copy the script and paste it in the onboard.sh file. 

  ![Copy and Paste Script](./Images/Copy%20and%20Paste%20Script.PNG)

  Use :w to save in vi and use :qa to exit vi.
  To do this, you need to press esc then shift + : then type w and enter. 


  ![Write and Save Script](./Images/Write%20and%20Save%20Script.PNG)


  Then shift + : then qa and enter to quit.

  ![Quit Script](./Images/Quit%20Script.PNG)


  To confirm that the contents of onboard.sh were saved run the command below.

  `cat onboard.sh`

  ![Confirm onboard.sh contents](./Images/Confirm%20onboard.sh%20contents.PNG)


  You can continue using gitbash if you want. In my case I will switch to Powershell. Run `cd downloads` to change directory to downloads folder.


  ![Directory Change Downloads](./Images/Directory%20Change%20Downloads.PNG)


  To confirm that you can see the onboard.sh file in downloads using the Powershell terminal, run `cat onboard.sh` as you did previously in gitbash.


  ![Confirm onboard.sh Contents Powershell](./Images/Confirm%20onboard.sh%20Contents%20Powershell.PNG)


  Now we want to use the SCP command to copy the onboard.sh file to the Ubuntu EC2 instance using the command below. Note that the pem key that we used to to EC2 stored in downloads folder is *DevOps_Projects.pem* and the public address right now is *3.141.201.108*. Public address will change.

  `SCP -i DevOps_Projects.pem onboard.sh ubuntu@3.141.201.108:~/;`

  You will see that the onboard.sh has been copied to the server successfully.


  ![Onboard.sh Copied to Server](./Images/Onboard.sh%20Copied%20to%20Server.PNG)


Now connect to the remote server (EC2 server) through SSH client as you have been connecting before.


![Connect to EC2 Screen 1](./Images/Connect%20to%20EC2%20Screen%201.PNG)


![Connect to EC2 Screen 2](./Images/Connect%20to%20EC2%20Screen%202.PNG)


Go to the terminal and copy the Example link and press Enter to connect.


![Copy Example Link Connect](./Images/Copy%20Example%20Link%20Connect.PNG)


![EC2 Connected ](./Images/EC2%20Connected.PNG)


Run `ls -l` to show the onboard.sh that we copied over initially.


![Confirm onboard.sh file](./Images/Confirm%20onboard.sh%20file.PNG)


Now let's go to the documentation and follow the steps below.

1. Create the project folder called Shell.


   `mkdir Shell`


   ![Create Shell](./Images/Create%20Shell.PNG)


   Move into the Shell folder


   `cd Shell`


   ![Directory Change Shell](./Images/Directory%20Change%20Shell.PNG)


2. Move onboard.sh to the shell folder by running the command below.

   * Run `cd ..` to go back to the previous directory.


     ![Previous Directory](./Images/Previous%20Directory.PNG)

    * You can run the command below to move onboard.sh to the Shell folder.

      `mv onboard.sh /home/ubuntu/Shell/`

      ![Move onboard.sh Shell](./Images/Move%20onboard.sh%20Shell.PNG)


      Change directory to Shell by running `cd Shell`.


      ![Directory Change Shell 2](./Images/Directory%20Change%20Shell%202.PNG)


      Run `ls` to confirm onboard.sh is in Shell folder.


      ![Confirm onboard.sh_Shell](./Images/Confirm%20onboard.sh_Shell.PNG)





3. Create id_rsa , id_rsa.pub and names.CSV files by the command below.


   `touch id_rsa id_rsa.pub names.CSV`

   ![Create files](./Images/Create%20files.PNG)


4. Open the id_rsa.pub file using your favorite editor and paste in the public key. Note: Go to the documentation to copy the public key.


   `vi id_rsa.pub`

   Below is the public key.
   
   ```
   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCzKZyicHxIkklSrNlxsJyyTrcIdBIt84Z0cQb3R4k0jH53kxkaT5hP8tfWTe62LXi7vV86fY+SX7TBNM76XGCbw/6vrMGegm6J1x2i1AiLNwq5nqTjOGn0AIwku4IlCCLAB7tdfRyVuCarmBlwny3lzRyybIUAWXR/D6vpN09MsDILbKdhay+Q/p9OUBMSLPqXdY/QIh/Oe3rVv1lwY3AohNfq7V3tO88zKswfA5iiexNiSYX1myT0OrX8cBE771j9quoNZhQgaLI1mIMtAvnHQChrn9k2nUaO/BMBCQGol5XzGv1ado7hgoVPoluIUD+FGNo/pH4zcmDLICH6drXY/C9MESnkMUPLFxBXKO/OitApY71vRao9nAhAwpVMsy6FqiOb5uawhvhoHYIHTV/f4EtagVagRMP2PxYMYR6jykIV4MPJTkCm+lGhTyMlRu+qRQjdLn8AAtHf4aEV8dIkoGh088DI7eA/4o0wz4OV4upH5ewSFS+5IHmRECEW5Nc=
   ```
   Write, Save and quit by pressing;

      * i for insert
      * esc
      * Shift :
      * wqa

    Run `cat id_rsa.pub to confirm it is saved.

    ![Confirm id_rsa.pub](./Images/Confirm%20id_rsa.pub)




5. Open the id_rsa file using your favorite editor and paste in the public key. Note: Go to the documentation to copy the private key.



```
-----BEGIN OPENSSH PRIVATE KEY-----
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
kmaSGj362OnCCNAAAACWRhcmVARGFyZQE=
-----END OPENSSH PRIVATE KEY-----
```

Write, Save and quit by pressing;

      * i for insert
      * esc
      * Shift :
      * wqa

Run `cat id_rsa to confirm it is saved.


![Confirm id_rsa](./Images/Confirm%20id_rsa.PNG)


Open the names.csv file.

`vi names.csv`

Insert some random names into it. (One name per line). Press i to insert.

* Jakomala
* Jarongo
* Omondi
* Onyango
* Omollo
* Achieng
* Sibuor
* Otieno
* Anyango
* John 

Write, Save and quit by pressing;

      * i for insert
      * esc
      * Shift :
      * wqa


![Names Added](./Images/Names%20Added.PNG)




Run `cat names.CSV` to confirm it is saved.


![Confirm names.csv](./Images/Confirm%20names.csv)


Run `pwd` to get the path.


![Shell Path](./Images/Shell%20Path.PNG)


We now need to edit the onboard.sh file. Open the script using

 `vi onboard.sh`.

 Press i to insert. Scroll to the bottom. We need to update the rsa.pub to the authorized keys to avoid the script from failing.


![Edit Script 1](./Images/Edit%20Script%201.PNG)


Replace /root/onboard/ with /home/ubuntu/Shell


![Updated Script 1](./Images/Updated%20Script%201.PNG)


Write, Save and quit by pressing;

      * esc
      * Shift :
      * wqa



Run `cat onboard.sh` to confirm it is saved.


![Confirm onboard.sh updated file](./Images/Confirm%20onboard.sh%20updated%20file.PNG)


Run `sudo groupadd developers`


![Add developers group](./Images/Add%20developers%20group.PNG)


Before we run the script as an SSH file we need to make it an executable by running;

  `sudo chmod +x onboard.sh`


![Make onboard.sh Executable](./Images/Make%20onboard.sh%20Executable.PNG)


Now try to run the script without sudo previledge by running the command `./onboard.sh` . It should let you know that :Only Admin can Onboard A User" as shown below.


![No Sudo Priviledge](./Images/No%20Sudo%20Priviledge.PNG)


Switch to a root user by running the command below.

`sudo su`

![Root User Switch](./Images/Root%20User%20Switch.PNG)


Run the script again.

`./onboard.sh`

The script ran successfully.

![Script Ran Successfully](./Images/Script%20Ran%20Successfully.PNG)


Run `ls -l /home/`. It will show that the users have been created.


![Users Created](./Images/Users%20Created.PNG)


We need to check that the developers group has been created by running the command below.' It will should that the developers group has been created with id of 1001


`getent group developers`


![Confirm developers group created](./Images/Confirm%20developers%20group%20created.PNG)


Run the command below you will see the information of the users created.

`cat /etc/passwd`


![Information of user created](./Images/Edit%20Script%201.PNG)



We can use the awk command below to filter the output data.

`cat /etc/passwd | awk -F':' '{ print $1}' | xargs -n1 groups`


![AWK Command  Output](./Images/AWK%20Command%20Output.PNG)



Test a few of the users randomly, and ensure that you are able to connect to the server using the private key and the public key.


Open a new windor for the terminal. In my case I will open gitbash since the downloads directory does not run some commands like vi.


![Git Bash Terminal](./Images/Git%20Bash%20Terminal.PNG)



Change to downloads directory.

`cd downloads`


![Directory Change Downloads 2](./Images/Directory%20Change%20Downloads%202.PNG)



Lets assume that one of the users is a developer and has been provided a priovate key to connect to the server (18.188.57.105). The same provate key that we used for the project.

Note that in the script we copied the public key that the provate key recognizes. id_rsa.pub is the key pair for the for the private key that we are trying to use to connect.

Create a file aux-proj.pem that you will use to store the provate key by running the command below. 


`vi aux-proj.pem`


![Aux-proj.pem](./Images/Aux-proj.pem.PNG)


Copy and paste the private key to the file.


```
-----BEGIN OPENSSH PRIVATE KEY-----
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
kmaSGj362OnCCNAAAACWRhcmVARGFyZQE=
-----END OPENSSH PRIVATE KEY-----
```


Write, Save and quit by pressing;

      * i for insert
      * esc
      * Shift :
      * wqa



![Aux-proj-private.pem](./Images/Aux-proj-private.pem.PNG)



Run `cat aux-proj.pem` to confirm it is saved.


![Confirm aux-proj.pem](./Images/Confirm%20aux-proj.pem.PNG)




Let's try to connect now.

Go back to your old terminal and run `ls /home/` to provide you a list of users.


![List of users](./Images/List%20of%20users.PNG)



Select any user that you would like to connect with. In our case we will choose *Rachel*


Run the command below. Note that the Public IP address is 18.188.57.105 and you can get it from the EC2 instance.


`ssh -i aux-proj.pem Rachel@18.188.57.105`


![Rachel Connected](./Images/Rachel%20Connected.PNG)

It shows that we are able to connect to the EC2 server as Rachel who is one of the developers we added.


Let's check if Rachel has a sudo priviledge. Rachel will be prompted for password. Use *password* and it will show that Rachel is not in the sudoers file. This incidence will be reported. Run;

`sudo apt update`


![Rachel sudo check](./Images/Rachel%20sudo%20check.PNG)


As a developer you have restrictions in authorizations that you have . In this case Rachel does not have sudo.


Run `ls -la` to see what access rachel has.



![Rachel Access](./Images/Rachel%20Access.PNG)


It shows that .ssh folder is there. Let's see what is in .ssh folder.

`ls -la .ssh/`


![.ssh folder contents](./Images/ssh%20folder%20contents.PNG)



It will show that authorized_key files is there as we had specified.


Complete.






















































