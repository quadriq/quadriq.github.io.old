---
layout: post
title:  "Angular2 and WordPress API Headers"
comments: true
date:   2016-09-02 12:00:00 +0000
permalink: /:year/:month/:day/:title/
---

By developing the Angular2 App its usual running on localhost... e.g. `localhost:5555` or `localhost:3000`, depending on your development config

If you are using [json-api](https://wordpress.org/plugins/json-api/other_notes/) plugin for WordPress and trying to retrieve something like `GET www.wp-example.host?json=get_recent_post` from you test localhost Angular2 instance, you would probably run into this kind of error:

```
No 'Access-Control-Allow-Origin' header is present
```

To slove this we need to tell WP Server to response with `Access-Control-Allow-Origin` header. To do this edit (or create) `.htaccess` file in your root WP dir (usually its `/var/www/html`) with this content:

```
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]

Header add Access-Control-Allow-Origin "*"
Header add Access-Control-Allow-Headers "origin, x-requested-with, content-type"
Header add Access-Control-Allow-Methods "PUT, GET, POST, DELETE, OPTIONS"

</IfModule>

```

Do not forget to ensure, that apache has Header module enabled:

```bash
sudo a2enmod headers
sudo service apache restart
```

> note: If you are using Docker as WP Container, do not restart apache server, but restart docker container

And here the  code of the method, I am using in Angular2 Component to get WP List of Posts:

```java
export const contentHeaders = new Headers();
contentHeaders.append('Accept', 'application/json');
contentHeaders.append('Content-Type', 'application/json');

private getPostsList() {
    this.http.get('http://wp.host?json=get_recent_posts', {headers: contentHeaders})
      .map(res => res.json())
      .subscribe(
      response => {
        this.posts = response.posts;
        console.log(this.posts);
      },
      error => {
        alert(error.text());
        console.log(error.text());
      }
      );
  }
```
