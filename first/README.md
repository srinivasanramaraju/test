**Problem**

Our applications deployed on multiple servers need to read a configuration file from the local directory on the server. Each server name is of the following format:

`<env>-<deployment_id>-<application_name>-<instance_num>.glassdoor.local`

An example of server name would be *qa-20180918-app-02.glassdoor.local*. The names of all servers running in the data center are ideally available through API calls but for this problem, we will assume the file `servers.txt` present in this folder provides us with that.

The configuration file needed by the applications consist of the following properties:
1. Installation directory for the application of the format `/opt/<application_name>` 
2. Environment in which the application would run.
3. The port numbers on which the instances of the application runs*

*There is a direct correlation between the instance number (from the server name) and the number of ports the application runs on, starting with port number `1000`. 

For example, the server with name *qa-20180918-app-02.glassdoor.local* (instance number 02), would be running the app on two ports `1000` and `1001` as evident from the sample *config.yaml* file present in this folder.

And a server with name  *qa-20180918-main-03.glassdoor.local* (instance number 03) would be running the app on three ports `1000`, `1001` and `1002`.

**Goal**

Write a standalone script in bash, python, ruby (or any other popular language native to automation frameworks like Chef, Ansible, etc.) to:
* Accept and parse the below user inputs
* Construct and place the configuration file at `/opt/<application_name>/config.yaml` on all servers of interest
* Start the application assuming it is already configured to be managed as `<application_name>.service`

Assume that the necessary credentials are available and configured to connect to these servers via SSH, from the host where this script would be executed by developers.
 
**User Inputs**
1. Environment
2. Deployment ID
3. Application Name