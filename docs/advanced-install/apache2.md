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
   ```
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

6) At line 72, you should see #LoadModule and this will continue until line 178. Delete all the LoadModule lines and replace them with the content here

```
LoadModule access_compat_module modules/mod_access_compat.so
LoadModule actions_module modules/mod_actions.so
LoadModule alias_module modules/mod_alias.so
LoadModule allowmethods_module modules/mod_allowmethods.so
LoadModule asis_module modules/mod_asis.so
LoadModule auth_basic_module modules/mod_auth_basic.so
#LoadModule auth_digest_module modules/mod_auth_digest.so
#LoadModule auth_form_module modules/mod_auth_form.so
#LoadModule authn_anon_module modules/mod_authn_anon.so
LoadModule authn_core_module modules/mod_authn_core.so
#LoadModule authn_dbd_module modules/mod_authn_dbd.so
#LoadModule authn_dbm_module modules/mod_authn_dbm.so
LoadModule authn_file_module modules/mod_authn_file.so
#LoadModule authn_socache_module modules/mod_authn_socache.so
#LoadModule authnz_fcgi_module modules/mod_authnz_fcgi.so
#LoadModule authnz_ldap_module modules/mod_authnz_ldap.so
LoadModule authz_core_module modules/mod_authz_core.so
#LoadModule authz_dbd_module modules/mod_authz_dbd.so
#LoadModule authz_dbm_module modules/mod_authz_dbm.so
LoadModule authz_groupfile_module modules/mod_authz_groupfile.so
LoadModule authz_host_module modules/mod_authz_host.so
#LoadModule authz_owner_module modules/mod_authz_owner.so
LoadModule authz_user_module modules/mod_authz_user.so
LoadModule autoindex_module modules/mod_autoindex.so
#LoadModule buffer_module modules/mod_buffer.so
#LoadModule cache_module modules/mod_cache.so
#LoadModule cache_disk_module modules/mod_cache_disk.so
#LoadModule cache_socache_module modules/mod_cache_socache.so
#LoadModule cern_meta_module modules/mod_cern_meta.so
LoadModule cgi_module modules/mod_cgi.so
#LoadModule charset_lite_module modules/mod_charset_lite.so
#LoadModule data_module modules/mod_data.so
#LoadModule dav_module modules/mod_dav.so
#LoadModule dav_fs_module modules/mod_dav_fs.so
#LoadModule dav_lock_module modules/mod_dav_lock.so
#LoadModule dbd_module modules/mod_dbd.so
LoadModule deflate_module modules/mod_deflate.so
LoadModule dir_module modules/mod_dir.so
#LoadModule dumpio_module modules/mod_dumpio.so
LoadModule env_module modules/mod_env.so
#LoadModule expires_module modules/mod_expires.so
#LoadModule ext_filter_module modules/mod_ext_filter.so
#LoadModule file_cache_module modules/mod_file_cache.so
LoadModule filter_module modules/mod_filter.so
LoadModule headers_module modules/mod_headers.so
#LoadModule heartbeat_module modules/mod_heartbeat.so
#LoadModule heartmonitor_module modules/mod_heartmonitor.so
#LoadModule http2_module modules/mod_http2.so
#LoadModule ident_module modules/mod_ident.so
#LoadModule imagemap_module modules/mod_imagemap.so
LoadModule include_module modules/mod_include.so
#LoadModule info_module modules/mod_info.so
LoadModule isapi_module modules/mod_isapi.so
#LoadModule lbmethod_bybusyness_module modules/mod_lbmethod_bybusyness.so
#LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so
#LoadModule lbmethod_bytraffic_module modules/mod_lbmethod_bytraffic.so
#LoadModule lbmethod_heartbeat_module modules/mod_lbmethod_heartbeat.so
#LoadModule ldap_module modules/mod_ldap.so
#LoadModule logio_module modules/mod_logio.so
LoadModule log_config_module modules/mod_log_config.so
#LoadModule log_debug_module modules/mod_log_debug.so
#LoadModule log_forensic_module modules/mod_log_forensic.so
#LoadModule lua_module modules/mod_lua.so
#LoadModule macro_module modules/mod_macro.so
LoadModule mime_module modules/mod_mime.so
#LoadModule mime_magic_module modules/mod_mime_magic.so
LoadModule negotiation_module modules/mod_negotiation.so
LoadModule proxy_module modules/mod_proxy.so
#LoadModule proxy_ajp_module modules/mod_proxy_ajp.so
#LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
#LoadModule proxy_express_module modules/mod_proxy_express.so
#LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so
#LoadModule proxy_ftp_module modules/mod_proxy_ftp.so
#LoadModule proxy_html_module modules/mod_proxy_html.so
LoadModule proxy_http_module modules/mod_proxy_http.so
#LoadModule proxy_http2_module modules/mod_proxy_http2.so
#LoadModule proxy_scgi_module modules/mod_proxy_scgi.so
#LoadModule proxy_wstunnel_module modules/mod_proxy_wstunnel.so
#LoadModule ratelimit_module modules/mod_ratelimit.so
#LoadModule reflector_module modules/mod_reflector.so
#LoadModule remoteip_module modules/mod_remoteip.so
#LoadModule request_module modules/mod_request.so
#LoadModule reqtimeout_module modules/mod_reqtimeout.so
LoadModule rewrite_module modules/mod_rewrite.so
#LoadModule sed_module modules/mod_sed.so
#LoadModule session_module modules/mod_session.so
#LoadModule session_cookie_module modules/mod_session_cookie.so
#LoadModule session_crypto_module modules/mod_session_crypto.so
#LoadModule session_dbd_module modules/mod_session_dbd.so
LoadModule setenvif_module modules/mod_setenvif.so
#LoadModule slotmem_plain_module modules/mod_slotmem_plain.so
#LoadModule slotmem_shm_module modules/mod_slotmem_shm.so
#LoadModule socache_dbm_module modules/mod_socache_dbm.so
#LoadModule socache_memcache_module modules/mod_socache_memcache.so
LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
#LoadModule speling_module modules/mod_speling.so
LoadModule ssl_module modules/mod_ssl.so
#LoadModule status_module modules/mod_status.so
#LoadModule substitute_module modules/mod_substitute.so
#LoadModule unique_id_module modules/mod_unique_id.so
#LoadModule userdir_module modules/mod_userdir.so
#LoadModule usertrack_module modules/mod_usertrack.so
#LoadModule version_module modules/mod_version.so
#LoadModule vhost_alias_module modules/mod_vhost_alias.so
#LoadModule watchdog_module modules/mod_watchdog.so
#LoadModule xml2enc_module modules/mod_xml2enc.so
```

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
