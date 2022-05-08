# Intermediate-Linux-Admistration
This is about SSH, OPENSSH server, SUDOERS(How to configure sshd configuration files), how to securely upload and download files, and process management ( /fg | /bg| /job)


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
                                    
3. ## 
