# Google Authentication with Passport.js in Node.js

This guide walks you through the process of setting up **Google Authentication** in a Node.js application using **Passport.js**.

---

## Prerequisites  
- Node.js installed on your machine  
- A Google account to create OAuth credentials  

---
## Getting Started  

### 1. Create a New Node.js Project  
Open your terminal and navigate to the desired directory.  
Run the following commands to create a new project:

mkdir google-auth-passport  
cd google-auth-passport  
npm init -y  
### 2. Install Required Dependencies
Install the necessary packages for the application:


npm install express passport passport-google-oauth20 express-session dotenv
### 3. Set Up Google OAuth Credentials
Go to the Google Cloud Console.

Create a new project.

Navigate to APIs & Services > Credentials.

Click on Create Credentials and select OAuth 2.0 Client IDs.

Configure the OAuth consent screen (if required).

Set the Authorized redirect URIs to:

http://localhost:3000/auth/google/callback
After creating the credentials, note down the Client ID and Client Secret.

### 4. Create a .env File
In the root directory of your project, create a .env file and add your Google credentials:


GOOGLE_CLIENT_ID=your-google-client-id  
GOOGLE_CLIENT_SECRET=your-google-client-secret  

### 5. Set Up the Express Application
5.1. Create the app.js File
Create a new file named app.js in the root of your project.

5.2. Load Environment Variables
Add this line at the top of your app.js file to load the credentials from the .env file:

require('dotenv').config();

5.3. Import Required Packages

const express = require('express');
const session = require('express-session');
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;

5.4. Initialize Express

const app = express();

5.5. Set Up Express Session Middleware

app.use(
  session({
    secret: 'your-secret-key',
    resave: false,
    saveUninitialized: true,
  })
);

5.6. Initialize Passport Middleware

app.use(passport.initialize());
app.use(passport.session());

5.7. Configure Google OAuth Strategy

passport.use(
  new GoogleStrategy(
    {
      clientID: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET,
      callbackURL: '/auth/google/callback',
    },
    (accessToken, refreshToken, profile, done) => {
      // Save user profile to the database (if needed)
      return done(null, profile);
    }
  )
);

5.8. Set Up Serialize and Deserialize Functions

passport.serializeUser((user, done) => {
  done(null, user);
});

passport.deserializeUser((user, done) => {
  done(null, user);
});

5.9. Define Routes

app.get('/', (req, res) => {
  res.send('<h1>Home</h1><a href="/auth/google">Login with Google</a>');
});

app.get(
  '/auth/google',
  passport.authenticate('google', { scope: ['profile', 'email'] })
);

app.get(
  '/auth/google/callback',
  passport.authenticate('google', { failureRedirect: '/' }),
  (req, res) => {
    res.redirect('/profile');
  }
);

app.get('/profile', (req, res) => {
  res.send(
    `<h1>Profile</h1><pre>${JSON.stringify(req.user, null, 2)}</pre>`
  );
});

5.10. Start the Server

app.listen(3000, () => {
  console.log('Server is running on http://localhost:3000');
});

### 6. Run the Application
Start the server with:

node app.js
Visit http://localhost:3000 in your browser.

### 7. Test the Authentication
Click on Login with Google on the home page.
Authorize the application on Google's OAuth page.
After successful login, you'll be redirected to the /profile route, displaying your user profile.

### Troubleshooting & Customization
Callback URL Error: Ensure that the redirect URI in the Google Cloud Console matches http://localhost:3000/auth/google/callback.
Storing User Data: Modify the GoogleStrategy callback to save the user's profile in a database (e.g., MongoDB).
Session Configuration: Use a session store like express-mysql-session or Redis for production environments.

### Contributing
Feel free to submit issues or pull requests for improvements.

