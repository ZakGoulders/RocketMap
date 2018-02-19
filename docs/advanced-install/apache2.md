# Apache2 Reverse Proxy

If you do not want to expose RocketMap to the web directly or you want to place it under a prefix, follow this guide:

Assuming the following:

 * You are running RocketMap on the default port 5000
 * You've already made your machine available externally

1. Install [Apache2](https://httpd.apache.org/docs/current/install.html) -- plenty of tutorials on the web for this.
2. Enable the mods needed
   ```
   sudo a2enmod proxy proxy_http proxy_connect ssl rewrite
   ```
3. Create a file /etc/apache2/sites-available/rocketmap.conf
   ```
   sudo nano /etc/apache2/sites-available/rocketmap.conf`
   ```
   copy pasta:
   ```
   <VirtualHost *:80>

       ServerName rocketmap.yourdomain.com

       ProxyPass / http://127.0.0.1:5000/
       ProxyPassReverse / http://127.0.0.1:5000/

       RewriteCond %{HTTP_HOST} !^rocketmap\.yourdomain\.com$ [NC]
       RewriteRule ^/$ http://%{HTTP_HOST}/ [L,R=301]

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined

   </VirtualHost>

   <VirtualHost *:443>

       ServerName rocketmap.yourdomain.com

       ProxyPass / http://127.0.0.1:5000/
       ProxyPassReverse / http://127.0.0.1:5000/

       RewriteCond %{HTTP_HOST} !^rocketmap\.yourdomain\.com$ [NC]
       RewriteRule ^/$ http://%{HTTP_HOST}/ [L,R=301]

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined

       SSLCertificateFile /var/www/ssl_keys/yourcert.crt
       SSLCertificateKeyFile /var/www/ssl_keys/yourkey.key
       SSLCertificateChainFile /var/www/ssl_keys/yourintermediatecert.crt

   </VirtualHost>
   
   If you want your maps at `rocketmap.yourdomain.com`, keep it just like it is
   If you want your maps at `yourdomain.com/go/` (note the trailing slash!)
   ```
   (take out ServerName)
   ProxyPass /go/ http://127.0.0.1:5000/
   ProxyPassReverse /go/ http://127.0.0.1:5000/

   RewriteCond %{HTTP_HOST} !^yourdomain\.com/go/$ [NC]
   RewriteRule ^/go/$ http://%{HTTP_HOST}/go/ [L,R=301]
   ```
4. Test your Apache2 config: `sudo apachectl configtest`
5. Enable your new config: `sudo a2ensite rocketmap`
6. Reload your Apache2 service: `service apache2 reload`
7. You can now access it by going to: `http(s)://yourdomain.com/go` or `http(s)://rocketmap.yourdomain.com`

If you'd like to install Apache2 on Windows, follow this guide.

This includes the installation of Apache as well as the configs needed. 

1) Go to https://www.apachelounge.com/download/ and download the correct version for you depending on if your system is 32 or 64 bit.

2) Make a folder in Local Disk (C:) called Apache24 and put all the folders and of the extracted zip into there. It should contain 11 folders and 8 text documents.

3) You now need to add Apache to path. Find Enviroment Variables on your computer (All I needed to do  on Windows 10 was search enviroment variables and click on what comes up) and then click the Enviroment Variables. In the system variables part, scroll down to path and double click it. Click 'New' and type in C:\Apache24 then click OK and then repeat but this time, type in C:\Apache24\bin and then click OK and leave environment variables.

4) Go to C:\Apache24 and then open conf. Use notepad++ to edit httpd.conf (DON'T USE NOTEPAD). Here's where we start to do lots of editing (how fun)!

5) On line 37, make sure you have this ServerRoot "c:/Apache24" and on line 57 and 58, make sure you have this #Listen 12.34.56.78:80 on line 57 and this Listen 80 on line 58.

6) At line 72, you should see #LoadModule and this will continue until line 178. Delete all the LoadModule lines and replace them with the content in here http://textuploader.com/dhvuv

7) On line 504, it should say #Include conf/extra/httpd-vhosts.conf but it needs to say Include conf/extra/rocketmap.conf and save it.

8) Go to the extra folder and make a new file called rocketmap.conf and paste this into it replacing yourdomain.com with your relevant info.
   ```
<VirtualHost *:80>

    ServerName yourdomain.com

    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    
    RewriteCond %{HTTP_HOST} !^yourdomain\.com$ [NC]
    RewriteRule ^/$ http://%{HTTP_HOST}/ [L,R=301]

    ErrorLog logs/error.log
    CustomLog logs/access.log combined

</VirtualHost>
   ```
9) Now open cmd and run httpd -S and it should give you some information like ServerRoot and Main DocumentRoot. In the same cmd window, type httpd.exe -k install and it should install Apache.

10) In your RM config, comment out your port and host (this is because default is 127.0.0.1:5000 so it will redirect that to your website name).

11) You need to go to your router info (you will have done this when you port forwarded) and set up port forwarding for port 80 AND port 5000. 

12) Now run your scanner and it should be working!
