----------------------------
- APACHE mod_security README
----------------------------

--------------------------
- PURPOSE
--------------------------

The Apache module "mod_security" allows us to apply a security layer on top of Apache without making any uptime core changes.
It can protect Apache from various different security vulnerabilities and attacks, such as XSS (Cross Site Scripting) attacks.
For example, if you try the following URL on one of your uptime installs it will output the php session id right to the screen just by the small javascript script. Imagine a malicious ajax’y script to send that session to a remote server.. not good.

URL:
http://uptime-hostname:9999/index.php?m=<script>alert(document.cookie)</script>

mod_security allows us to detect and block this without any core/java changes.

--------------------------
- INCLUDED FILES
--------------------------

README.txt                         - this text file
modules/mod_security2.so           - mod_security module
modules/mod_unique_id.so           - mod_unique_id module (required for mod_security)
mod_security_default_rules.tar.gz  - contains default rules against known security attacks


--------------------------
- FILE INSTALLATION
--------------------------

Place the *.so files in the <uptime_dir>/apache/modules directory. On Linux the location is "/usr/local/uptime/apache/modules".
Place the "mod_security_default_rules.tar.gz" file in to the <uptime_dir>/apache/conf directory and extract it:
> tar -zxvf mod_security_default_rules.tar.gz
It should create a directory named "modsecurity_rules" with a bunch of .conf files in it. These are the rule files that mod_security will use.
the modsecurity.conf file is used to configure mod_security as well. The defaults should be enough to work with up.time.

--------------------------
- APACHE CONFIGURATION
--------------------------

Edit the file <uptime_dir>/apache/conf/httpd.conf and add the following section to the bottom of the file (for simplicity):

# Load mod_security module
LoadModule unique_id_module modules/mod_unique_id.so
LoadModule security2_module modules/mod_security2.so
<IfModule mod_security2.c>
    # set the default action to deny access if a rule is triggered (403: Forbidden error)
    SecDefaultAction phase:2,deny,status:403,log,auditlog
    # include all *.conf files in the modsecurity_rules directory
    Include /usr/local/uptime/apache/conf/modsecurity_rules/*.conf
</IfModule>


--------------------------
- TESTING
--------------------------

Enter the following in a browser, replacing "uptime-hostname" with the hostname:port of your up.time monitoring station:

http://uptime-hostname:9999/index.php?m=<script>alert(document.cookie)</script>

If you get a "Forbidden" error message that is confirmation that mod_security is setup and working properly.

To confirm if the module is loaded, create a file in "<uptime_dir>/GUI" named phpinfo.php and place the following line in the file:
<? phpinfo(); ?>

Then just access the page with the URL:
http://uptime-hostname:9999/phpinfo.php
