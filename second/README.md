**Problem 1**

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


**Problem 2**

We need automation to deploy a java based web application on some servers along with some configuration. The application WAR file would be available for download from a remote repository like Nexus, Artifactory, etc. of your choice.

As part of the configuration required, we need to install java and a web server like Tomcat, jetty, etc. and place a file containing credentials under the `/opt/apps/` directory on each of the servers. A sample *credentials.yaml* file has been provided in this folder.

**Goal**

Use an automation framework like Chef, Ansible, etc to do the following:
1. Install java (version 7 or greater) 
2. Install a web server like Tomcat, jetty, etc.
3. Download and place the WAR file in the configured location.
4. Place the credentials necessary for the application.

**Developer Configurable Attributes**
* Java version
* Web server version
* Remote Maven Repository URL
* Maven attributes for WAR file (groupId, artifactId, version)
* Web Server Installation Directory (WAR file location on server)
* Set up systemd service components for this web app

**Notes**
* If a list of available servers is needed, please use the *servers.txt* file in this folder.
* Keep security of credentials in mind and follow best practices
* State any additional assumptions you make.

**Out of scope**

The following tasks are not to be considered for the scope of this problem.
* Logging configuration
* Monitoring the application or system
* Setting up remote repository
* Validating credentials


**Solution**

The solution for both first and second problem are written as individual roles and combined them to run single ansible play book.

Below are the description of each role.

1. configure - Parses input from servers.txt under *glassdoor hosts group* and configures the config.yaml (problem 1)
2. Java - Install's the required version of Java, set's up the java service systemd file and places the secret
3. tomcat - Install's the  required version of the tomcat 
4. deploy - Searches for the WAR file from nexus and places it the given application directory

All the developer attributes and inputs for both the problems can be defined in `appvars/main.yml`

Note: As I'm unsure how the application reads the secrets, I have only restricted file permissions. I would prefer using ansible-vault for encryption based on application's usage.

*Assumptions*
1. All the required yum repo's have been pre configured in the server for installing neccessary packages.
2. The servers have to be grouped under glassdoor hosts group for the playbook to identify list of hosts
3. The server names are of the same format for the both the problems `<env>-<deployment_id>-<application_name>-<instance_num>.glassdoor.local`.
4. Ansible installed with version higher than >=2.4.0. (Tested in ansible 2.7.5)

*Command to Run the playbook*
```ansible-playbook -i ./servers.txt playbook.yml --extra-vars "@appvars/main.yml"```