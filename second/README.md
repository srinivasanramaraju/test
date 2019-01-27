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