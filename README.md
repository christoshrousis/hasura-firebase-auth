# Firebase Authentication with Hasura Sample App

This is a sample app to demontrate how to use Firebase Auth service to handle the authentication flow (generate JWT token, manage token expiration and renew, reset password, etc ) and configure Hasura to validate the token.

### Technologies used:

1. React (https://reactjs.org/)
2. Apollo Client (https://www.apollographql.com/docs/react/)
3. Apollo Link State (state management) (https://www.apollographql.com/docs/react/essentials/local-state.html)
4. Antd components for the UI (https://ant.design/docs/react/introduce)
5. Firebase Functions (https://firebase.google.com/docs/functions/?gclid=EAIaIQobChMIl_nZw7zi4AIVwgOGCh3ujwUhEAAYASAAEgJiYfD_BwE)
6. Firebase Hosting (https://firebase.google.com/docs/hosting/)
7. Firebase Auth (https://firebase.google.com/docs/auth/?gclid=EAIaIQobChMIusbY1rzi4AIVDlmGCh1IUgm0EAAYASAAEgJ3WPD_BwE)
8. Hasura (https://hasura.io/)
9. Hasura JWT authentication (https://docs.hasura.io/1.0/graphql/manual/auth/jwt.html)

### Installation Instructions

1. Install Hasura on Heroku. Click on the 1 one click install link below and follow the steps to create it.
   https://heroku.com/deploy?template=https://github.com/hasura/graphql-engine-heroku

2. Configure Hasura instance on Heroku to enable JWT token for Firebase Auth: Inside the Heroku dashboard, in Settings - Config Var add the following Config Vars

```
HASURA_GRAPHQL_ACCESS_KEY   blablabalalbablabalabl
HASURA_GRAPHQL_ENABLE_CONSOLE true
HASURA_GRAPHQL_JWT_SECRET {"type":"RS512","jwk_url": "https://www.googleapis.com/service_accounts/v1/jwk/securetoken@system.gserviceaccount.com"}
```

3. Click on Open App, add the Access Key (set in the previous step) to access Hasura Console, as an admin.

4. Create user table in Hasura. Select SQL option, paste this SQL Statement and click on the Run button:

```
CREATE TABLE "user" ("firebase_id" text NOT NULL UNIQUE, "name" text NOT NULL, "email" text NOT NULL UNIQUE, PRIMARY KEY ("firebase_id") );
```

This will create a user table on PostgreSQL, with fields name, email and firebase_id.

5. Clone this project from github

```
git clone https://github.com/leoalves/hasura-firebase-auth.git
```

6. Install Firebase cli. With the Firebase cli you will be able to authenticate, configure your project, deploy the functions and the app.

```
npm install -g firebase-tools
```

7. Go to the Firebase Console in the browser:
   https://console.firebase.google.com

8. Create a new Firebase Project

9. In the Firebase Project click in the hosting tab to enable hosting

10. In the Firebase Console change the Plan from Free to Blaze - pay as you go (The free plan does not allow requests to a url outside gCloud. You need to enable the Blaze plan to make external requests. You won't be charged, the FREE limits in the FREE account are still valid for the Blaze Account. But you need to enter your Billing Details.

11. In the Firebase Console click on Authentication - Set the sign-in method and enable Email/Password

12. Initiate your Firebase Project in the project root

```
cd hasura-firebase-auth
firebase init
```

13. Follow the wizard:
```
    Are you ready to proceed? Y
    Which Firebase CLI features do you want to setup? (Select Hosting and Functions)
    Are you ready to proceed? Y
    What language would you like to use to write Cloud Functions? Select Typescript
    Do you want to use TSLint to catch probable bugs and enforce style? Y
    File functions/package.json already exists. Overwrite? N
    File functions/tsconfig.json already exists. Overwrite? N
    File functions/src/index.ts already exists. Overwrite? N
    File functions/.gitignore already exists. Overwrite? N
    Do you want to install dependencies with npm now? Y
    What do you want to use as your public directory? ./app/build
    Configure as a single-page app (rewrite all urls to /index.html)? Y
    File /app/public/index.html already exists. Overwrite? N
```
14. Change the project to the new one you just created

```
firebase use --add [new-project-id]
```

15. Go to the functions folder

```
cd functions
```

16. Install dependencies

```
npm install
```

17. Change the functions config url to the Heroku graphql instance you just created.
    Open the file 
    ```./functions/src/config/graphql.ts``` 
    and change the graphql_host variable to the new graphql endpoint.
    You can get the Graphql endpoint in the Hasura Console where the GraphiQL is. Also change the hasuraAccessKey variable to the one defined in the env vars in Heroku (step 2).

18. Deploy the Firebase function to create the user ( If on windows rename the firebase.json from the root directory from `$RESOURCE_DIR` with `%RESOURCE_DIR%`)

```
firebase deploy --only functions
```

19. Open ```/app/src/config/config.ts``` and change the firebaseUrl variable with the function url returned from the firebase deploy command

20. In the same file (```/app/src/confg/config.ts```) change the graphqlUrl and subscriptionUrl with the Hasura Graphql endpoint.

21. Change the Firebase Web Config settings in the React App. Go to the Firebase console (https://console.firebase.google.com).
    ```
    Select your project.
    Select Settings
    Project Settings
    Under Your Apps section.
    Select the web icon and copy the Firebase config variable
    replace the config variables in the file /app/src/firebase/index.ts, with the config info from your project
    ```

22. Go to the app folder. Install dependencies and build the React App

```
cd app
```

23. Install Dependencies

```
npm install
```

24. Build Production App

```
npm run build
```

25. Deploy the Build React App to Firebase Hosting

```
firebase deploy --only hosting
```

26. The Firebase cli will output a url to access the deployed app. Copy the link and paste in your browser.

27. You should be able to create new users, login, logout and reset the password.
