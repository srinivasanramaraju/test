**Problem**

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

I have written the solution of both first and second problem as roles and combined them to run single ansible play book.

1. configure - Parses input from servers.txt under *glassdoor hosts group* and configures the config.yaml (problem 1)
2. Java - Install's the required version of Java and sets up the java service systemd file
3. tomcat - Install's the  required version of the tomcat 
4. deploy - Searches for the WAR file from nexus and places it the given application directory


All the developer attributes and inputs for both the problems can be defined in `appvars/main.yml`

*Assumptions*
1. All the required yum repo's have been pre configured in the server for installing neccessary packages.
2. The servers have to be grouped under glassdoor hosts group for the playbook to identify list of hosts
3. The server names are of the same format for the both the problems `<env>-<deployment_id>-<application_name>-<instance_num>.glassdoor.local`.
4. Ansible installed with version higher than >=2.4.0. (Tested in ansible 2.7.5)

*Command to Run the playbook*
```ansible-playbook -i ./servers.txt playbook.yml --extra-vars "@appvars/main.yml"```