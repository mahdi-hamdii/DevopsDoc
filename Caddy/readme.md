## Caddy

- When caddy is installed and configured as system service, a default /etc/caddy/Caddyfile can be used as the global configuration file, and a sub-directory with a suggested name /etc/caddy/sites to contain the configuration files for individual sites, akin to apache and nginx configuration.
- Caddyfile:

```yaml
{
    log default {
       format console
       output file /var/log/caddy/system.log
       exclude http.log.access
    }
}

import config/*
import sites/*
```

- This configures Caddy to write system logs to /var/log/caddy/system.log file but HTTP request logs, as well as loading additional configuration files from config and sites directories.
- Caddy is integrated with PHP-FPM in a similar way to how nginx and apache are, using FastCGI reverse proxy.
- When Caddy receives a request that should be processed with PHP, the request is sent to a PHP-FPM, where the PHP application is executed, and the response is sent back to Caddy to return to the user.
`/etc/caddy/sites/example.com.conf`
```yaml
example.com {

    root * /var/www/example.com/public

    log {
        output file /var/log/caddy/example.access.log
        format console
    }

    # Encode responses in zstd or gzip, depending on the
    # availability indicated by the browser.
    encode zstd gzip

    # Configures multiple PHP-related settings
    php_fastcgi unix//run/php/php-fpm.sock

    # Prevent access to dot-files, except .well-known
    @dotFiles {
      path */.*
      not path /.well-known/*
    }
}

```
- `php_fastcgi` takes one argument for the PHP-FPM server. it can be a server address and a port (such as 127.0.0.1:9000)
- **URL rewriting to index.php**:
    1. rewrite requests to the file /index.php if it exists: if a request arrives at example.com/test and if a file exists at test/index.php, caddy rewrites this request to the test/index.php file. this resolves the `trailing slash problem`, where a php application exists inside a sub-directory of the document root.
    2. Rewrite to `index.php` if a file does not exist: The second thing configured with `php_fastcgi` is that caddy tries to serve the request with a file if it exists. For example, if the user requests example.com/image.png, and if a file named image.png exists in the document root, Caddy serves it as a file without invoking PHP at all. Then if a file does not exist, it tries to look for an index.php in the path as a directory, followed by an attempt to rewrite it to the root index.php
    3. Pass .php files to PHP-FPM: `php_fastcgi` directive routes .php files to the specified FPM server address. Caddy knows to properly set the FPM parametersn split the path, and perform several other "handover tasks"