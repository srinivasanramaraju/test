***Solution***

The solution for both first and second problem are written as individual roles and combined them to run single ansible play book.

Below are the description of each role.

1. configure - Parses input from servers.txt under *glassdoor hosts group* and configures the config.yaml (problem 1)
2. Java - Install's the required version of Java and places the secret
3. tomcat - Install's the  required version of the tomcat 
4. deploy - Searches for the WAR file from nexus and places it the given application directory

All the developer attributes and inputs for both the problems can be defined in `appvars/main.yml` or could be passed as --extra-vars

Note: As I'm unsure how the application reads the secrets, I have only restricted file permissions. I would prefer using ansible-vault for encryption based on application's usage.

*Assumptions*
1. All the required yum repo's have been pre configured in the server for installing neccessary packages.
2. The servers have to be grouped under glassdoor hosts group for the playbook to identify list of hosts
3. The server names are of the same format for the both the problems `<env>-<deployment_id>-<application_name>-<instance_num>.glassdoor.local`.
4. Ansible installed with version higher than >=2.4.0. (Tested in ansible 2.7.5)
5. Tomcat version needs to be a string. Split() functionality has been used to identify versions.

*Command to Run the playbook*

```ansible-playbook -i ./servers.txt playbook.yml --extra-vars "@appvars/main.yml"```