# php-recipe
## Installing and code-signing PHP

## Homebrew

First install Homebrew using the formula:

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`

For reference:  https://wpbeaches.com/installing-homebrew-on-macos-big-sur-11-2-package-manager-for-linux-apps/

And Run these two commands in your terminal to add Homebrew to your PATH: (NB.  edit the user_name)

`(echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/user_name/.zprofile`
    
`eval "$(/opt/homebrew/bin/brew shellenv)"`

Then you can check for install issues by running:

`brew doctor`

**On Intel Macs Homebrew can be found at:**

`/usr/local/homebrew`

**And on Apple Silicon Macs Homebrew lives at:**

`/opt/homebrew`

You can check the Homebrew config by running:

`brew config`

To remove Homebrew and all the packages it has installed use:

`/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"`



## PHP

First add the PHP formulae:

`brew tap shivammathur/php`

Then choose the PHP version (e.g. 8.3)

`brew install shivammathur/php/php@8.3`

Then link the PHP version:

`brew link --overwrite --force php@8.3`

**NOTE:**

Apple's default apache is found at:

`/etc/apache2`

==> Caveats
==> php@8.3
To enable PHP in Apache add the following to httpd.conf and restart Apache:

    LoadModule php_module /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so


    ```
    <FilesMatch \.php$>
        SetHandler application/x-httpd-php
    </FilesMatch>
    ```

Finally, check DirectoryIndex includes index.php

    DirectoryIndex index.php index.html

The php.ini and php-fpm.ini file can be found in:
 
   **ARM**
    
   `/opt/homebrew/etc/php/8.3/`
    
   **Intel**
    
   `/usr/local/etc/php/8.3/`

php@7.4 is keg-only, which means it was not symlinked into /opt/homebrew,
because this is an alternate version of another formula.

If you need to have php@8.3 first in your PATH, run:

  **ARM**
  
  `echo 'export PATH="/opt/homebrew/opt/php@8.3/bin:$PATH"' >> ~/.zshrc`
  
  `echo 'export PATH="/opt/homebrew/opt/php@8.3/sbin:$PATH"' >> ~/.zshrc`
  
   **Intel**
   
  `echo 'export PATH="/usr/local/cellar/php@8.3/bin:$PATH"' >> ~/.zshrc`
  
  `echo 'export PATH="/usr/local/cellar/php@8.3/sbin:$PATH"' >> ~/.zshrc`
  
For compilers to find php@8.3 you may need to set:
  **ARM**
  
  ```
  export LDFLAGS="-L/opt/homebrew/opt/php@8.3/lib"
  export CPPFLAGS="-I/opt/homebrew/opt/php@8.3/include"
  ```
  
   **Intel**
   
  ```
  export LDFLAGS="-L/usr/local/cellar/php@8.3/lib"
  export CPPFLAGS="-I/usr/local/cellar/php@8.3/include"
  ```

To switch between versions after an upgarde:

  `brew link --overwrite --force php@8.3`

or,

`brew link --overwrite --force php@8.3`
  
To restart shivammathur/php/php@8.3 after an upgrade:

  `brew link --overwrite --force php@8.2`
  
Or, if you don't want/need a background service you can just run:

  **ARM**
  
  `/opt/homebrew/opt/php@8.3/sbin/php-fpm --nodaemonize`

  **Intel**
  
  `/usr/local/opt/php@8.3/sbin/php-fpm --nodaemonize`


Restart Terminal and check the version:

`php -v`

To change to another version just repeat the process from the brew install... then unlink and link in the new PHP version by issuing a command like below but with your correct version e.g.:
brew unlink php && brew link --overwrite --force php@8.3

NB The php.ini and php-fpm.ini file can be found in:

   **ARM**
    
    /opt/homebrew/etc/php/8.2/
    
   **Intel**
   
   /usr/local/etc/php/8.3/
    

## PEAR

This FileMaker Engineering Blog discusses how to install PEAR on macOS.
It is required for FileMaker Server but as far as I can tell you can skip this for munkireport.

https://support.claris.com/s/answerview?anum=000035470&language=en_US#a2



## Apache

To enable PHP in Apache add the following to httpd.conf and restart Apache:

   **ARM**
    
   `LoadModule php_module /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so`
    
   **Intel**
    
   `LoadModule php_module /usr/local/opt/php@8.3/lib/httpd/modules/libphp.so`
   
    
   ```
   <FilesMatch \.php$>
       SetHandler application/x-httpd-php     
   </FilesMatch>
   ```

Finally, check DirectoryIndex includes index.php

    DirectoryIndex index.php index.html

Restart Apache:

`sudo apachectl -k restart`

The php.ini and php-fpm.ini file can be found in:

   **ARM**
    
    /opt/homebrew/etc/php/8.3/
    
   **Intel**
   
    /usr/local/etc/php/8.3/


If you have not code-signed php yet you will see this error:

[so:error] [pid 26552] 
AH06665: No code signing authority for module at /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so specified in LoadModule directive.
httpd: Syntax error on line 188 of /private/etc/apache2/httpd.conf: 
Code signing absent - not loading module at: /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so


## CodeSigning PHP
You can check the location of your php with this command:

`grep -nir "^loadmodule.*php" /etc/apache2`

It should return:

`/etc/apache2/other/00-httpd.conf:4:LoadModule php_module /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp7.so`

Now you can code sign that using your Apple Developer ID.
Similar to: 

https://derflounder.wordpress.com/2019/04/10/notarizing-automator-applications/

e.g.

`codesign --force --options runtime --deep --sign "Developer ID Application: Name Here (YG45FDT45F)" "/path/to/Application Name Here.app"`

Eg:

**ARM**

`codesign --force --options runtime --deep --sign "Developer ID Application: Example.com (X3Q1C2345)" "/opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so"`

**Intel**

`codesign --force --options runtime --deep --sign "Developer ID Application: Example.com (X3Q1C2345)" "/usr/local/Cellar/php@8.3/8.3.1/lib/httpd/modules/libphp.so"`



Verify the code signed signature:

`codesign -dv --verbose=4 "/path/to/Application Name Here.app"`

`codesign -dv --verbose=4 "/opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so"`

Add the code signing certificate name after the module path in apache's http.conf LoadModule Directive:

`LoadModule php_module /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so "Signing Certificate Name"`

**ARM**

`LoadModule php_module /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so "Example.com (X3Q1C2345)"`

`LoadModule php_module /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so "Developer ID Application: Example.com (X3Q1C2345)"`


**Intel**

`LoadModule php_module /usr/local/opt/php@8.3/8.3.1/lib/httpd/modules/libphp.so "Developer ID Application: Example.com (ABCDE1234)"`

Restart Apache:

`sudo apachectl -k restart`

If it worked you should now see:

[so:notice] [pid 27274] 
AH06662: Allowing module loading process to continue for module at /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so because module signature matches authority "Developer ID Application: Example.com (ABCDE1234)" specified in LoadModule directive


Test PHP by placing the following phpinfo.php file in the default home directory of your server at:

`/Library/WebServer/Documents/phpinfo.php`

NB. The file should be removed after testing for security reasons

```
<?php
// Show all information, defaults to INFO_ALL
phpinfo();
?>
```

And view it at:

http://localhost/phpinfo.php


## Backup and Restore

The Apache HTTP.conf file could get overwritten by Apple on security or OS updates.

You can backup and restore the http.conf file with the commands in my reverse proxy tutorial.
Or you can do it manually.


**To backup:**

```
#!/bin/zsh
#Set the variables
THEFILE="/private/etc/apache2/httpd.conf"
APACHE2_LOC="/private/etc/apache2"
# Backup httpd.conf
sudo cp "${APACHE2_LOC}/${HTTPD_FILE}" "${BACKUP_LOC}${HTTPD_FILE}"
```

**To restore it after the update using:**

```
#!/bin/zsh
#Set the variables
THEFILE="/private/etc/apache2/httpd.conf"
APACHE2_LOC="/private/etc/apache2"
#Restore httpd.conf"
sudo cp "${BACKUP_LOC}${HTTPD_FILE}" "${APACHE2_LOC}/${HTTPD_FILE}"
sudo chown root "${APACHE2_LOC}/${HTTPD_FILE}"
```

## Resources

How to future proof your apache modules in macOS by signing them with your own certificate authority - Camden Narzt

https://blog.phusion.nl/2020/12/22/future_of_macos_apache_modules/

Installing PHP 7 on macOS 12 Monterey - Tim Perfitt

https://twocanoes.com/knowledge-base/installing-php-7-on-macos-12-monterey/

Signing homebrew PHP module in macOS with your own signing authority

https://www.simplified.guide/macos/apache-php-homebrew-codesign

Signing Mac Software with Apple Developer ID

https://developer.apple.com/developer-id/

PHP no longer bundled with FileMaker Server - Claris Engineering Blog

https://support.claris.com/s/answerview?anum=000035470&language=en_US#a2

