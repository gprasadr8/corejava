### Configuring JMX for Apache Tomcat (no authentication):
  

 1. Add below java options to Tomcat by edit the catalina.bat or catalina.sh
    
		*Java Options:*

		```	-Dcom.sun.management.jmxremote

			-Dcom.sun.management.jmxremote.port=8008

			-Dcom.sun.management.jmxremote.authenticate=false

			-Dcom.sun.management.jmxremote.ssl=false
		```

 2. Save the changes and restart Tomcat

### Configuring JMX for Apache Tomcat (with Authentication):

 1. On your Tomcat host browse to  `%CATALINA_BASE%\conf`  directory

 2. In the conf directory, create a new blank text file and save it with the name  **jmxremote.access**

 3. In the conf directory, create another blank text file and save it with the name  **jmxremote.password**

 4. Edit the file **jmxremote.access** with a text editor and add the below text and save the changes (where  **jmxuser**  is the name of the user that will connect to the JMX listener)
		``` jmxuser readonly```

 5. Edit the file **jmxremote.password** with a text editor and define a password for your **jmxuser** (replace  **TheTestPwd123**  with a password of your choosing)
		```jmxuser  TheTestPwd123```
	

 6. Next, open a command prompt and browse to the folder where jmxremote.password is located

 7. Setup the required file permissions for the user who is running the tomcat server
		``` chmod 600 jmxremote.password```
			
 8. Add Java Options
		``` -Dcom.sun.management.jmxremote  
			-Dcom.sun.management.jmxremote.port=8008  
			-Dcom.sun.management.jmxremote.authenticate=true  
			-Dcom.sun.management.jmxremote.ssl=false  
			-Dcom.sun.management.jmxremote.password.file=/path/to/tomcat/conf/jmxremote.password
			-Dcom.sun.management.jmxremote.access.file =/path/to/tomcat/conf/jmxremote.access
		```

 9. Restart Apache Tomcat for the changes to take affect.

 10. If your accessing throgh the network need to add one more java option
	 ``` -Djava.rmi.server.hostname=hostname/ip```

 