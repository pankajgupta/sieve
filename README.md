# Website

This is the codebase of SEE client app. Below is the installation guide which will help us to setup this codebase on our local machine.


### System Requirements
- Nginx
- Node (v6.x.x with NPM 3.x.x)
- Ruby (v2.x.x)
- Git
- fontforge [Link] [font-forge]

We will move to the next steps after having these above list of dependencies installed & confirming that they are correctly running on our machine.


### Installation
Once we have Ruby setup, we will install `scss-lint` globally for linting our scss files.

```sh
$ gem install scss_lint
```

We need to ***fork*** this repository under our own account. It will create a personal copy of this repository. Once done, we can download the repository using git as below
```sh
git clone <OUR_REPOSITORY_URL>
```

After we have forked & downloaded the code locally, we will install the npm modules
```sh
$ cd see-client
$ npm i
```
### Building the code
We are following the distribution based build system, where every changes made to the source files, generates a final deployable asset files inside the `dist/` folder. Thus, the `dist/` folder can act as the root for the web server which will serve the entire web app.

To create a development version of compiled js & css files from the `src/`, we will run the following commands in order
```sh
npm run build
```

The default environment is `development`. We can create the build for different environments by passing the `process.env.NODE_ENV` values to the above commands. For eg, to create a build for `production` environment, we will run the following commands:
```sh
NODE_ENV=production npm run build
```

While working on the code, we would want to keep auto compiling the code on every change made to source files. For that to happen, will be build the code under `watch` mode. The below 2 commands have to be runned in 2 separate shell window since watch mode doesn't kill the ongoing process
```sh
npm run build:watch
```

### Setup NGINX server block
We will create a new file (if not already present) called `see.conf` & copy the below server block code for seeclient.io & paste/replace it in the see.conf file. The see.conf file should be either in `conf.d/` or in `servers/` folder where Nginx is installed

```sh
## Nginx config for seeclient.io (local dev site for stayzilla's website)
## our http server at port 80

server {
    listen      80;
    server_name  www.seeclient.io seeclient.io;

    ## Gzip Settings
    gzip on;
    gzip_vary on;
    gzip_static on;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_types text/plain text/css application/json application/javascript application/x-javascript text/javascript;


    ## Path where the desktop code resides
    root <SEE_CLIENT_CODE>/dist;


    ## Log Files
    access_log /var/log/nginx/sz_io_access.log;
    error_log /var/log/nginx/sz_io_error.log;
    error_page  404              /404.html;
    location = /404.html {
        root   /usr/share/nginx/html;
    }


    ## Redirect server error pages to the static page /50x.html
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }


    ## Root
    location / {
        try_files $uri /index.html;
    }


    ## To mock the CDN setup on local as it is on dev/preprod/prod environment
    location ~ ^/desktop/(.*) {
        rewrite ^/desktop/(.*) /$1?$args;
    }


    location ~* \.(js|css|png|jpg|jpeg|gif|ico)*$ {
        expires max;
        add_header Cache-Control public;
        access_log off;
        if (-f $request_filename) {
            break;
        }
    }
}
```
We also need to replace `<SZ_WEB_APP_CODE>` in the _see.conf_ file with the absolute path of our code folder.
***Example:***
```
root /Users/jigar/Sites/website/dist
```
Once done, we will save this file & we will `restart` the Nginx. Also we will modify our `/etc/hosts` file by adding the following entry to it
```
255.255.255.255 broadcasthost // Lines already present
::1             localhost // Lines already present
127.0.0.1       seeclient.io // You need to add this line
127.0.0.1       www.seeclient.io // You need to add this line
```

After making these changes, we should be able to access <http://seeclient.io> & see a running version of our site

### Tests
We use [Jest](https://facebook.github.io/jest "Jest") for testing our reducers, actionCreators & models. Below command will run all the test suites
```sh
npm run test
```

### Code Styles & Linting
This code uses ES2015 JS Syntax which is later transpiled by babel to conform to browser supported syntax.

We use [ESLint](http://eslint.org "ESLint") for linting our `.js/.json/.jsx` files and [SCSS-Lint](https://github.com/brigade/scss-lint "SCSS-Lint") for linting our `.scss` files.
```sh
$ npm run lint
```


### License

MIT

[font-forge]: <https://fontforge.github.io/en-US/>
