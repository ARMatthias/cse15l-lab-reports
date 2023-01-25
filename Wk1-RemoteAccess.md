
# Remote Access Tutorial
![image](https://user-images.githubusercontent.com/122485183/212784392-0ae62375-48a4-4380-9e5b-c106879caafa.png)
---


## Installing Visual Studio Code


In order to download and install VS Code, visit the [VS Code site](https://code.visualstudio.com/) and click **Download** (it should already be set to the download of your computer's operating system).

![image](https://user-images.githubusercontent.com/122485183/212784414-0107e32f-5861-44df-97de-b0ecac56de98.png)


If an alternate version is needed, use the dropdown acessible by the **arrow** on the left of the default download button to select the correct version. (This tutorial exemplifies remote access setup and execution on Windows OS)

![image](https://user-images.githubusercontent.com/122485183/212784439-99995548-d323-4ddd-bed6-55d7b87a528d.png)

After download is complete, open the executible and follow the setup wizard's instructions to install (for the purposes of this tutorial, all default settings work fine).


## (Windows Users) Installing Git


Download and insteall Git by visiting the [Git for Windows site](https://gitforwindows.org/) and click on **Download**

![image](https://user-images.githubusercontent.com/122485183/212784459-56311996-9df3-4f71-a2c1-c7f028c91a97.png)


Once download is complete, open the Git executible and complete the wizard's setup steps to finalize installation (default settings also work fine).


## Remote Connection

First, open Visual Studio Code, and you may be greeted by an interface like this:

![image](https://user-images.githubusercontent.com/122485183/212784474-0572b227-9c3f-471b-8945-d626a3c8d4cf.png)


Open a terminal using the keyboard shortcut by pressing **Control** and **` (backtick)**.

![image](https://user-images.githubusercontent.com/122485183/212784509-cbf5c774-0d67-4649-a165-de1b4b0801af.png)

Your default terminal may be set to something other than gitbash (the above depicted terminal is powershell, as indicated by **powershell** seen to the right of **TERMINAL** at the top of the terminal window).


Open a gitbash terminal by using the dropdown, accessible via the arrow immediately to the right of the **+ (plus)** next to the terminal title (in this example, **powershell**).

![image](https://user-images.githubusercontent.com/122485183/212784544-111acb54-24aa-45b1-82ee-60070065c238.png)


The new gitbash terminal should open awaiting a command.

![image](https://user-images.githubusercontent.com/122485183/212779482-2532ea5d-4e1e-4e3f-a26f-044c21011e87.png)

Type into the terminal

'ssh cs15lwi23**ZZZ**@ieng6.ucsd.edu'

Where the "**ZZZ**" are to be replaced with your unique letter combination to your CS15 student account. (Seen below in the next step, my unique letters are "**aao**") 
the command **ssh** stands for **S**ecure **Sh**ell, which is a safe protocol by which a user of an unsecure network may access a different computer. 
Submit the command to the terminal by hitting **Enter** on your keyboard.


If this is your first time connecting with the network, you will get a message like this in terminal:

```
The authenticity of host 'ieng6.ucsd.edu (128.54.70.227)' can't be established.
RSA key fingerprint is SHA256:ksruYwhnYH+sySHnHAtLUHngrPEyZTDl/1x99wUQcec.
Are you sure you want to continue connecting (yes/no/[fingerprint])? 
```

Type and enter "**yes**" 


The terminal will then prompt for a password. Type and submit your password (which is the same password as your CS15 student account password) to the terminal. (The cursor will not move while you are typing -- this is fine, as the terminal is still recording your keystrokes)

![image](https://user-images.githubusercontent.com/122485183/212780370-56cb6a38-9cc9-41ab-af03-2ae53cdee658.png)


A successful login will look something like this:

![image](https://user-images.githubusercontent.com/122485183/212783654-2634a354-c8e3-4df8-a0b9-6d068783a44c.png)


## Entering Commands


Now that you are successfully remotely connected to another computer in the network, navigate it using terminal commands!

Useful commands are as follows:
* **cd** : **c**hange **d**irectory. In order to navigate between folders and directories, prefix the desired folder like: 

    * `cd cs15lwi23aao`
    * `cd ..`
    * `cd ~`

where **.. (two periods)** moves into the parent directiory, and the **~ (tilde)** returns to the home directory.

![image](https://user-images.githubusercontent.com/122485183/212789271-c8bc0660-b97a-423b-9496-43a4c02924aa.png)

(note that a change of directory does not print anything to terminal. If there is no output for a cd, it means the change of directory was successful. Otherwise, there will be an error message printed to terminal in the event that an input destination doesn't exist or isn't accessible by the current working directory, which looks like:)

![image](https://user-images.githubusercontent.com/122485183/212789454-1d3b3576-8f0c-4d0e-bcd0-5b5f81beb3a7.png)

or an absolute path like:

      * `cd /home/linux/ieng6/cs15lwi23/cs15lwi23aao`

* **pwd** : **P**rint **w**orking **d**irectory, prints the absolute path of the current directory:

![image](https://user-images.githubusercontent.com/122485183/212787665-d61ad5f0-3332-49ad-ad59-5d253788cde1.png)

* **ls** : **L**i**s**ts the files and folders in the current directory: 

![image](https://user-images.githubusercontent.com/122485183/212787987-397da904-e410-4dc3-8d18-d9f9b835b760.png)

* **cat** : Con**cat**enates and prints the contents of one or more indicated files
* **cp** : **C**o**p**ies files from one path to an indicated destination
* **mkdir**: **m**a**k**es new **dir**ectory 

In order to log out of remote access, use the terminal command:

`exit`

And a successful log-out should come with a confirmation message prinnted to terminal, and a return to a default terminal:

![image](https://user-images.githubusercontent.com/122485183/212789647-d8c46631-c09f-4a51-b604-6fac60af17e9.png)
