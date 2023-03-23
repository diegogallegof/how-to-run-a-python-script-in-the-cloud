





How to run a Python script in the cloud?

[source](https://medium.com/@andras1000_18467/how-to-run-a-python-script-in-the-cloud-e486eef96ac3)

In this tutorial, I will show how to start your own cloud computer, run a Python script and keep it running even after you have shut down your laptop and gone to sleep.

I use Mac OS X, but this should work on Linux too. The cloud used is Amazon Web Services (AWS).

Pricing
You might be eligible for the free tier at AWS, otherwise the cost of running a basic cloud computer should be as low as $0.01 per hour. Note that you will need your credit card details to create an AWS account.

1. Launch your cloud computer
Below is a quick step-by-step guide to starting a cloud computer also called a cloud instance. There is a more detailed guide here.

Create an account at AWS
Log in to AWS
Choose EC2 from the Services menu
(Optional) In the top-right corner select the region that is closest to you.
Click Launch Instance
Choose Amazon Linux
Click Review and Launch and on the next page click Launch
You can select Create a new key pair in the pop-up window and give it any name you want.
Click Launch Instance
Save the .pem file somewhere you’ll remember. This is your AWS key and we will need it later
Click View Instances
After a short while you should see that a new instance is up and running. You can see this on the AWS browser page under Instances.

2. Connect to the cloud computer
Open the Terminal on Mac or Linux
You first need to change the access permissions of your AWS key file:
$ chmod 400 my-key-pair.pem
We now ssh into the cloud computer. This will create a secure connection between our laptop and the cloud
$ ssh -i /path/my-key-pair.pem ec2-user@public_dns_name
If all went well, you are now remotely logged in to your cloud computer.

3. Setup Python on the cloud
Your instance should come with Python 2.7 pre-installed. I will install Python 3 below. To see the Python-related packages available for install on your cloud computer type:

$ yum list | grep python
yum is a package manager for Linux and grep is a search command applied to the output of yum (this is what the | sign achieves). grep looks for anything with “python” in the output of yum.

To install Python 3:

$ sudo yum install python35
The sudo prefix ensures that the above command is run with administrator privileges, which is necessary to install stuff.

To install Python packages with pip (Python package manager), I also needed to run:

$ sudo yum install python35-devel
$ sudo yum install gcc
4. Set up a Python virtual environment
It is best practice to set up a Python virtual environment and install any new packages there instead of installing packages in the Python root.

To create a virtual environment for Python 3

$ virtualenv -p python3 venv
You can choose a name other then “venv” for your virtual environment.

To activate the virtual environment

$ source venv/bin/activate
You can later deactivate the virtual environment by typing

$ deactivate
As an example, we can now install the Python package scrapy, a webscraping framework, with pip install.

$ pip install scrapy
5. Run a Python script
Create a blank Python script:

$ vi test.py
Press i to enter insert mode and type the following code.

import time
for i in range(1000, -1, -1):
  print(“Time remaining: {}”.format(i))
  time.sleep(1)
To exit the vi editor, first press Esc to exit insert mode and then press :x. This will also save the file.

Run the script:

$ python test.py
6. Upload Python code from your own machine
If you would like to upload existing code from your machine, use the secure copy command below.

$ scp -ri ~/documents/path/yourkey.pem ~/documents/path/example/ ec2-user@ec2–23–21–11–38.eu-central-1.compute.amazonaws.com:/home/ec2-user
Replace the above two files paths with the path to your AWS key and the folder containing the Python code. You can find out the last parameter that is needed above by right-clicking on the instance you have just started and selecting Connect.

The option -r is for recursive copy as we are copying a complete folder and option -i stands for identity_file to provide the path to your AWS key.

If you are only uploading a single file, the -r option is not necessary.

7. Keep your Python script alive and running
To keep Python running even after you disconnect from the cloud instance we install tmux.

$ sudo yum install tmux
Start tmux

$ tmux
Run the Python script inside tmux

$ python test.py
Press CTRL + B and type :detach. This will exit tmux, but the Python script will still be running in the background. Disconnect from the cloud by typing.

$ ~.
Reconnect to the cloud.

$ ssh -i “~/documents/path/mykey.pem” ec2-user@ec2–23–21–59–38.eu-central-1.compute.amazonaws.com:/home/ec2-user
Remember to change the above parameters to the ones you are using.

Enter tmux and see the Python code at work:

$ tmux attach
At this point, you should see our Python script running.

To stop the script, press CTRL + B and type:

:kill-session
8. Stop the cloud computer
On the AWS browser page under Instances, right-click the instance you want to terminate, select Instance State and select Terminate. This is also when billing stops.

Conclusion
You should now be able to fire up a cloud computer, upload existing Python scripts, run them and have them running even after you have disconnected from the cloud.
