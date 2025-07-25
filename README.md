# DevTinder

- created a vite + react project using npm create vite@latest my-project -- --template react
- vite 7.0 works with node 20 +, since I have node v19, I have used vite 6.0

- installed tailwind css using (see its official page for installation)
    - npm install -D tailwindcss postcss autoprefixer
    - npx tailwindcss init -p

- install daisyUI : npm i -D daisyui@latest
- install React Router : npm i react-router-dom
- install redux toolkit using - npm i @reduxjs/toolkit react-redux


# Deployment

- signup on AWS
- launch instance
- chmod 400 <secret>.pem (write this cmd on terminal by going to the secret key path)
- run the ssh cmd to connect to the machine

// once you are on your new virtual machine
- download node.js using curl cmd (get the cmd from official nodejs page and the version should match with your machine)
- exit cmd will log out from the machine
- clone your backend and frontend code 

- go to the frontend code path
    - run npm install (install dependencies)
    - build the code - npm run build (dist folder will be created)
    - sudo apt update
    - sudo apt install nginx (we will use NGINX to host our frontend project)
    - sudo systemctl start nginx (to start nginx)
    - sudo systemctl enable nginx
    - scp -r dist/* /var/www/html/ (copy code from dist folder to /var/www/html/)

    If you get permission denied while copying, use the above cmd with sudo

    - you can check if it has copied using - cd /var/www/html/
    - Enable port :80 on your EC2 instance at AWS (Add inbound rule)
    - copy the ipv4 address from aws and run it on the browser

- go to the backend code path
    - npm install
    - npm run start

    if the database is not connecting, go to mongoDB cloud -> network access -> add your AWS IP

    - add inbound rule in AWS to add the database port (in our case 3000)
    - copy the ipv4 address from aws and run it on the browser with :3000 (e.g. 43.204.96.49:3000)

    running the server through terminal using npm start is temporary as if we close the terminal, the server will stop running. Therefore we will use PM2 package.

    PM2 - is a deamon process manager that will help you manage and keep your application online 24/7

    - npm install pm2 -g
    - pm2 start npm --name "devTinder-backend" -- start

    # few pm2 command
    - pm2 list
    - pm2 logs (to check the logs if your application is not running)
    - pm2 flush npm (to exit)
    - pm2 stop npm (to stop the server)
    - pm2 delete npm  (it will delete the process)
    - config nginx (see below connecting frontend with backend)
    - restart nginx - sudo systemctl restart nginx
    - copy the ipv4 address and run it on the browser with /api (e.g. 43.204.96.49/api/feed)
    - modify the BASE_URL in frontend project to /api

    if you change anything in the code do - git pull and repeat the steps from npm run build to copying dist files

# connecting frontend with backend using nginx proxy pass in nginx config

- sudo nano /etc/nginx/sites-available/default
    server_name AWS IP or if you have a domain name
    location /api/ {
        proxy_pass http://localhost:3000/; # pass the request to the Node.js app
        Proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
- save and exit

# adding a custom domain name
- you can use godaddy.com to purchase domain name
- setup DNS for your domain in godaddy.com by copying nameservers from cloudflare
- setup SSL certs, etc using Cloudflare
- wait for sometime till your nameservers are updated
- edit the DNS records in cloudflare
- edit the A record with AWS IP
- to make the website secure, go to SSL/TLS -> Custom SSL/TLS -> Flexible -> Save
- go to Edge Certificates -> Automatic https rewrites -> keep it enabled




