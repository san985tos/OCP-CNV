# Clone a Virtual Machine

In this lab we're going to clone a workload and see that it's identical to the source of the clone. To do this we'll complete the following steps:

- Using the console, get Fedore-original IP

  ![This is an image](images/1.png)

- Note the IP address

  ![This is an image](images/2.png)

- On bastion's CLI, run:

  ```
  curl IP:80
  ```

- Now, clone the VM

  ![This is an image](images/3.png)

- After cloned VM is up, try again curl command with clone VM IP !!

  ![This is an image](images/4.png)
