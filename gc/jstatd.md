**jstatd :**

The jstatd tool is an RMI server application that monitors for the creation and termination of instrumented HotSpot Java virtual machines (JVMs) and provides a interface to allow remote monitoring tools to attach to JVMs running on the local host.

The jstatd server requires the presence of an RMI registry on the local host. The jstatd server will attempt to attach to the RMI registry on the default port, or on the port indicated by the -p port option. If an RMI registry is not found, one will be created within the jstatd application bound to the port indicated by the -p port option or to the default RMI registry port if -p port is omitted. Creation of an internal RMI registry can be inhibited by specifying the -nr option.

**NOTE:** This utility is unsupported and may or may not be available in future versions of the JDK. It is not currently available on the Windows 98 and Windows ME platforms.

**jstatd OPtions:**

**-p  port:** Port number where the RMI registry is expected to be found, or, if not found, created if -nr is not specified.

**-Joption:** Pass option to the java launcher called by javac. For example, -J-Xms48m sets the startup memory to 48 megabytes. It is a common convention for -J to pass options to the underlying VM executing applications written in Java.

**SECURITY**
The jstatd server can only monitor JVMs for which it has the appropriate native access permissions. Therefor the jstatd process must be running with the same user credentials as the target JVMs. Some user credentials, such as the root user in UNIX™ based systems, have permission to access the instrumentation exported by any JVM on the system. A jstatd process running with such credentials can monitor any JVM on the system, but introduces additional security concerns.

The jstatd server does not provide any authentication of remote clients. Therefore, running a jstatd server process exposes the instrumentation export by all JVMs for which the jstatd process has access permissions to any user on the network. This exposure may be undesireable in your environment and local security policies should be considered before starting the jstatd process, particularly in production environments or on unsecure networks.

The jstatd server installs an instance of RMISecurityPolicy if no other security manager has been installed and therefore requires a security policy file to be specified. The policy file must conform to the default policy implementation's Policy File Syntax.

The following policy file will allow the jstatd server to run without any security exceptions. This policy is less liberal then granting all permissions to all codebases, but is more liberal than a policy that grants the minimal permissions to run the jstatd server.
```
grant codebase "file:${java.home}/../lib/tools.jar" {
   permission java.security.AllPermission;
};
```
To use this policy, copy the text into a file called jstatd.all.policy and run the jstatd server as follows:

`jstatd -J-Djava.security.policy=jstatd.all.policy`

For sites with more restrictive security practices, it is possible to use a custom policy file to limit access to specific trusted hosts or networks, though such techniques are subject to IP addreess spoofing attacks. If your security concerns cannot be addressed with a customized policy file, then the safest action is to not run the jstatd server and use the jstat and jps tools locally.

**Enabling RMI logging capabilities.**
This example demonstrates starting jstatd with RMI logging capabilities enabled. This technique is useful as a troubleshooting aid or for monitoring server activities.

`jstatd -p 2020 -J-Djava.security.policy=jstatd.all.policy -J-Djava.rmi.server.logCalls=true`

create `jstatd.all.policy` file with below code

```
grant codebase "file:${java.home}/../lib/tools.jar" {
  permission java.security.AllPermission;
};

```

then start running it by using below command 
   `jstatd -J-Djava.rmi.server.hostname=remotehostname/ip -J-Djava.security.policy=/path/to/jstatd.all.policy` 
 once we run, it will start jstatd in 1099 default port.

To check running process based on port in linux command: `ss -lptn 'sport = :1099'`
