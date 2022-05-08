# Intermediate-Linux-Admistration
This is about SSH, OPENSSH server, SUDOERS(How to configure sshd configuration files), how to securely upload and download files (SFTP - SCP), and process management ( /fg | /bg| /job)


1. ## What is SSH

**SSH** or ***Secure Shell** is a network communication protocol that enables two computers to communicate (c.f http or hypertext transfer protocol, which is the protocol used to transfer hypertext such as web pages) and share data. An inherent feature of ssh is that the communication between the two computers is encrypted meaning that it is suitable for use on insecure networks.

SSH is often used to "login" and perform operations on remote computers but it may also be used for transferring data.

![generate-ssh-keys-in-linux-01607975652](https://user-images.githubusercontent.com/80678596/167299788-3818060e-217c-44e8-85c9-9c4afb7eb8b4.png)


2. ## OPENSSH server ( How to connect to server from the client )

  I will be demostrating this using a virtual machine (Virtual box). I downloaded a virtualbox on my system and created two desktop. I called one `client` and the `remote` 
  
  I open the terminal and in other not to get confused with the two desktop terminals i change the hostname of each of the terminal using the command line below to open the file and i edited it to the name `client`. I did same for the other desktop i rename it to `remote`
  
                          sudo nano /etc/hostname
                          
After renaming the hostname, i reboot the the desktop to reflect the new name

                          sudo reboot now
                          
On my client desktop i list the ssh file. ( In my case, my user name is `segun` so in my command line i will be using segun as the user. if you are making reference to this documentation, use your user name)

                              ls -latr /home/segun/.ssh
                              
Next, we can check for the user id

                                        id
                                        
Next we generate a key pair on the client desktop (This key pair will be saved into the directory /home/segun/.ssh/id_rsa.pub)

                                        ssh-keygen
                                        
we can then list the directory to check that the key pair we ctreated is there

                                       ls -latr /home/segun/.ssh
                                       
We can cat in the file to see the private or the public key

                                         cat /home/segun/.ssh/id_rsa.pub
                                         
                                         cat /home/segun/.ssh/id_rsa
                                         
Next we run `ssh` to ensure that it is running 

                                                    ssh
                                                    
On the remote server desktop we will need to install openssh using the command line below (This will enable the ssh to communicate with the server from the remote)


                                          sudo apt install openssh-server -y                  
 
 Next use `ifconfig` to get the ip address of our remote desktop using the command ( we can run the command both )
 
                                           sudo apt install net-tools
                                                      
                                                      ifconfig

On the clinet server, send the public key from client to server using the the command below 

                                             ssh-copy-id -i /home/segun/.ssh/id_rsa.pub segun@<IP-Address of Server>
                                             
Next we can `ssh` into the remote remote server using the command below and boom, we will be login into the remote desktop from our client desktop

                                                ssh user@<IP-Address of Server>
                                                
We can exit the remote desktop by using the command below

                                                      exit
                                                      
On the remote server we can check all the log from the client user. We will use this command line 

                                    sudo tail -f /var/log/auth.log
                                  
On the client server we will run the command line below 

                                    ssh user@<IP-Address of server> -vvv
                                    
3. ## SUDOERS(How to configure sshd configuration files)


Now we will proceed to how to configure sshd configuartion files 

- Let create a user on one of the desktop server and give it a password (Let us use the user name tolu as an example)

                          sudo useradd -m tolu
                          
                          sudo passwd tolu
                          
- let us check if the user is created using the `id`

                                      id tolu
                                      
-  Now we will switch the user to the new created user 

                                    sudo  su tolu
                                    
                                    exit
                                    
**Note:** The new user we created do not have acces to use sudo or access the root file so we can edit the permission in the sudoer file

                                  ls -latr /etc/sudoers
                                  
                                  sudo nano /etc/sudoers
                                  
                            
**Note:** The new user we created do not have acces to use sudo or access the root file so we can edit the permission in the sudoer file

 We can use the command `sudo nano /etc/sudoers` to open the sudoer file and then add the user name and give it the root permission
 
 - Next we should always run this command to ensure that our filoe is okay

                                        sudo visudo -c
                                        
 - Next we can switch user into the the user tolu and we will not have restricted access again.

**This can be done by creating a new group and add the user to it**

   1. create a group name devops

                      sudo groupadd devops
                      
   2. Next we add the user tolu to the group created 

                      sudo usermod -a -G devops tolu
                      
   3. We can check again if the user has been ctrated 

                      id tolu
                      
   4. Let us check if the file is okay

                      sudo visudo - c
                      
   5. Next we switch user to the user tolu created

                      sudo su tolu

   **We can use the following Command line to control how root user can access the remote server**
   
                        ls -ltr /etc/ssh/sshd_config
                        
                        nano /etc/ssh/sshd_config
                        
                        sudo sshd -t
                        
                        sudo systemctl restart sshd
                                             

  3. ## How to securely upload and download files (SFTP - SCP)

 In our Client Server as we did above we will `mkdir` and put in files into the directory. let assume we create a directory call `images` and we put some files in the directory. we want to transfer the files fron the client desktop to the remote desktop using ssh
 
                                    sudo mkdir /Desktop/images
 
 - In our remote server we will make a directory in `/var/www/html` directory

                              sudo mkdir -p /var/www/html
                              
                              sudo ld -ltr /var/www/html
                              
 - On our remote server we can get our IP address using any of the following command 

                              hostname -i
                              
                              ifconfig
                              
 - We need to create the same `image` directoy we created in our client server in the directory we created in our remote serever 

                                         sudo mkdir /var/www/html/images

 - Next, we change the ownship of the directory we created 

                                          sudo chown user:user /var/www/html/images
                                          
 - On the client desktop we will login into the remote userver using `sftp` and we cd into the directory

                                            sftp <IP-Server>
                                            
                                            cd /var/www/html
                                            
 - Next we want to upload the folder from the client server to the remote server
 
                                             put -r images
                                           
 - Now if we want to download the the folder instead of upload then we will use the command line below

                                              get -r images
                                              
     **Let try using `SCP` instead of `SFTP`**
     
     
     - To upload with scp we use the command line below

                       scp -r <folder_name/> user@<Ip-Addrress of server>:/<folder we want to upload it to>
                                    
                                    
                                    
                   scp -r user@<Ip-Addrress of server>:/<folder/file to be downloaded> <absolute path to download the folder>
                                            
