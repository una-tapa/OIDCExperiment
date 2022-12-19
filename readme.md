# Experiment - OIDC and Bell Custom User Registry

This test is done on the Windowns Subsystem Linux so I had to use the ip address explicitly instead of localhost. 

## OP
OP directory has Liberty's OpenID Connect Provider configuration.
## RP
RP directory has Liberty's Replying Party (OpenID COnnect Client)configuration.   OP and RP work together. 
## OIDC login flow
The folloging is the flow of OIDC login
* As soon as I entered the application URL (http://172.23.96.138:19080/testpage) in the browser, I was redirected to the oidc login page.  The screenshot shows I am entering the user/password defined in the OpenID Connect Provider. 
   ![Tux, the Linux mascot](/images/oidcloginOnOP.png)

* Once the authentication at the OP is successful, I am asked for permission to share more information. 
   ![Tux, the Linux mascot](/images/oidc-questions.png)

* finally I was redirected back to the application server.  The application is showing the authenticated/authorized user. 
   ![Tux, the Linux mascot](/images/oidcsuccess.png)


## RPBell  

To the RP configuration, I added bell configuration in the Bell Custom User Registry.
Unfortunately my Bell Custom User Registry reads users from a file.  The file path is hardcoded and I could not place it on the test server due to the lack of privilege..  

If I finds time, I will update the user registry so it won't rely on the hardcoded file for user and groups.  For now I think this shows that bells user registry is visible and getting called when the oidc client is configured.  
```
Caused by: java.io.FileNotFoundException: /opt/ibm/wlp/usr/servers/defaultServer/resources/users.props (No such file or directory)
	at java.io.FileInputStream.open0(Native Method)
	at java.io.FileInputStream.open(FileInputStream.java:195)
	at java.io.FileInputStream.<init>(FileInputStream.java:138)
	at java.io.FileInputStream.<init>(FileInputStream.java:93)
	at java.io.FileReader.<init>(FileReader.java:58)
	at com.ibm.ws.samples.cur.FileRegistrySample.fileOpen(FileRegistrySample.java:893)
	at com.ibm.ws.samples.cur.FileRegistrySample.getUsers(FileRegistrySample.java:219)
```    

But I do see my Bell Custom User Registry `com.ibm.ws.samples.cur.FileRegistrySample` is accessed in the error stack. 
  
   ```
   [12/19/22 16:55:03:730 EST] 00000027 id=00000000 com.ibm.ws.security.registry.RegistryException               > <init> Entry  
                                                                                                               /opt/ibm/wlp/usr/servers/defaultServer/resources/users.props (No such file or directory)
                                                                                                               com.ibm.websphere.security.CustomRegistryException: /opt/ibm/wlp/usr/servers/defaultServer/resources/users.props (No such file or directory)
	at com.ibm.ws.samples.cur.FileRegistrySample.getUsers(FileRegistrySample.java:236)
	at com.ibm.ws.security.registry.internal.CustomUserRegistryWrapper.getUsers(CustomUserRegistryWrapper.java:211)
	at com.ibm.ws.security.wim.adapter.urbridge.URBridge.login(URBridge.java:1015)
	at com.ibm.ws.security.wim.ProfileManager.loginImpl(ProfileManager.java:1792)
	at com.ibm.ws.security.wim.ProfileManager.genericProfileManagerMethod(ProfileManager.java:254)
	at com.ibm.ws.security.wim.ProfileManager.login(ProfileManager.java:217)
	at com.ibm.ws.security.wim.VMMService.login(VMMService.java:249)
	at com.ibm.ws.security.wim.registry.util.LoginBridge.checkPassword(LoginBridge.java:116)
	at com.ibm.ws.security.wim.registry.WIMUserRegistry.checkPassword(WIMUserRegistry.java:157)
	at com.ibm.ws.security.authentication.jaas.modules.UsernameAndPasswordLoginModule.login(UsernameAndPasswordLoginModule.java:76)
	at com.ibm.ws.kernel.boot.security.LoginModuleProxy.login(LoginModuleProxy.java:51)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at javax.security.auth.login.LoginContext.invoke(LoginContext.java:755)
	at javax.security.auth.login.LoginContext.access$000(LoginContext.java:195)
	at javax.security.auth.login.LoginContext$4.run(LoginContext.java:682)
	at javax.security.auth.login.LoginContext$4.run(LoginContext.java:680)
	at java.security.AccessController.doPrivileged(Native Method)
	at javax.security.auth.login.LoginContext.invokePriv(LoginContext.java:680)
	at javax.security.auth.login.LoginContext.login(LoginContext.java:587)
    ```
     ![Tux, the Linux mascot](/images/belllogin.png)


## Bells custom user registry 

The Bells user registry is here. 
https://github.com/una-tapa/bellscur