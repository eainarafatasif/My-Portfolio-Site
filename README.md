# PortfolioV2

![PortfolioV2](/images/portfolio1.gif)

### A clean portfolio template. (Readme will be updated soon)

# Sections

- Home
- Education and Certificates
- Experience
- Projects
- Contact and Resume
### Installation

To deploy a React.js project using Nginx and MySQL on Ubuntu 22.04, follow these steps:

### 1. **Update and Upgrade System Packages**
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

### 2. **Install Nginx**
   ```bash
   sudo apt install nginx -y
   ```

### 3. **Install Node.js and npm**
   Install Node.js (make sure you have the correct version required by your React project):
   ```bash
   sudo apt install nodejs npm -y
   ```
   Check the installed versions:
   ```bash
   node -v
   npm -v
   ```

### 4. **Install MySQL**
   ```bash
   sudo apt install mysql-server -y
   ```
   Log in to MySQL and create a database for your React.js project if needed:
   ```bash
   sudo mysql -u root -p
   ```
   Inside the MySQL shell, run:
   ```sql
    CREATE DATABASE django_db;
    CREATE USER 'django_user'@'localhost' IDENTIFIED BY 'Pa$$word';
    GRANT ALL ON django_db.* TO 'django_user'@'localhost';
    FLUSH PRIVILEGES;
    EXIT
   ```

### 5. **Set Up a Backend to Interact with MySQL**
   You need a backend server to handle database operations. Here’s a basic setup using Node.js with Express:

   1. **Install Node.js and Express:**
      ```bash
      npm install express mysql
      ```

   6. **Create a Backend Server:**
      Create a `server.js` file in your project directory:
      ```javascript
      const express = require('express');
      const mysql = require('mysql');
      const app = express();
      const port = 5000;

      // Create connection to MySQL
      const db = mysql.createConnection({
          host: 'localhost',
          user: 'your_mysql_user',
          password: 'your_mysql_password',
          database: 'your_database_name'
      });

      // Connect to MySQL
      db.connect(err => {
          if (err) {
              throw err;
          }
          console.log('MySQL Connected...');
      });

      // Example route to get data from database
      app.get('/api/data', (req, res) => {
          let sql = 'SELECT * FROM your_table_name';
          db.query(sql, (err, results) => {
              if (err) throw err;
              res.send(results);
          });
      });

      app.listen(port, () => {
          console.log(`Server running on port ${port}`);
      });
      ```

   7. **Start the Backend Server:**
      ```bash
      node server.js
      ```

### 8. **Make API Calls from React.js**
   In your React.js frontend, you can make API calls to interact with the backend server, which in turn interacts with the MySQL database.

   Here’s how you can fetch data from the backend using `fetch` or `axios`:

   ```javascript
   import React, { useEffect, useState } from 'react';
   import axios from 'axios';

   function App() {
       const [data, setData] = useState([]);

       useEffect(() => {
           axios.get('/api/data')
               .then(response => {
                   setData(response.data);
               })
               .catch(error => {
                   console.error('There was an error fetching the data!', error);
               });
       }, []);

       return (
           <div className="App">
               <h1>Data from MySQL Database</h1>
               <ul>
                   {data.map((item, index) => (
                       <li key={index}>{item.name}</li>  // Adjust the field name based on your database schema
                   ))}
               </ul>
           </div>
       );
   }

   export default App;
   ```

### 9. **Proxy Requests to Backend in Development**
   If you're running your React app in development mode (e.g., using `npm start`), you can set up a proxy in the `package.json` file to forward requests to the backend:

   ```json
   "proxy": "http://localhost:5000"
   ```

### 10. **Deploy Backend and Frontend Together**
   When deploying, ensure that both the React frontend and the backend are correctly configured on the server. You can use tools like Nginx to reverse proxy requests to the appropriate service (React frontend or backend).


### 11. **Build the React.js Project**
   Navigate to your React.js project directory:
   ```bash
   cd /path/to/your/react-project
   ```
   Install dependencies:
   ```bash
   npm install
   ```
   Build the project:
   ```bash
   npm run build
   ```
   The build files will be generated in a `build` folder.

### 12. **Configure Nginx for React.js**
   Remove the default Nginx configuration:
   ```bash
   sudo rm /etc/nginx/sites-enabled/default
   ```
   Create a new Nginx configuration file:
   ```bash
   sudo nano /etc/nginx/sites-available/react-app
   ```
   Add the following configuration:
   ```nginx
   server {
       listen 80;
       server_name your_domain_or_ip;

       root /path/to/your/react-project/build;
       index index.html;

       location / {
           try_files $uri /index.html;
       }

       location /api/ {
           proxy_pass http://localhost:5000;  # Assuming your backend is running on port 5000
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```
   Enable the new configuration:
   ```bash
   sudo ln -s /etc/nginx/sites-available/react-app /etc/nginx/sites-enabled/
   ```

### 13. **Restart Nginx**
   ```bash
   sudo systemctl restart nginx
   ```

### 14. **Configure SSL (Optional)**
   If you need to secure your site with SSL, you can use Let's Encrypt:
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d your_domain_or_ip
   ```

### 15. **Check the Deployment**
   Visit your server's IP address or domain name in a browser to verify the deployment.
