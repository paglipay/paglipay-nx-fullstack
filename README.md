# Deploy a fullstack NX workspace on Heroku

[NX Workspaces](https://nx.dev/) is a powerful tool for scaffolding enterprise level starting points for node.js monorepos. In this post we will be scaffolding a simple React & Express app that we will deploy to [Heroku](https://www.heroku.com/). We will using yarn as package manager.

## Use NX cli to generate new React & Express app

Run the following command in your terminal.

```
yarn create nx-workspace --package-manager=yarn nx-fullstack
```

Or if you prefer npm

```
npx create nx-workspace nx-fullstack
```

#### Define you project configuration

You'll be greeted by this prompt. Select <code>react-express</code> in the CLI list. Name your application <code>nx-fullstack</code>. Select <code>styled-components</code> as your styling solution. Select if you want sign up for the NX Cloud. The CLI will now scaffold your project.
![NX CLI](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/p3jv8s8vjctp4gut8a7p.png)

#### Run your newly generated app locally

Cd in to your newly created folder with the command <code>cd nx-fullstack</code>. Inside the folder run

```
yarn nx run-many --target=serve --projects=nx-fullstack,api --parallel=true
```

Wait for the client <code>nx-fullstack</code> and the backend <code>api</code> to start. Go to http://localhost:4200 in your browser. Confirm that the client app at <code>apps/nx-fullstack/src/app/app.tsx</code> is talking to the backend at <code>apps/api/src/main.ts</code>. You should see the text <code>Welcome to the api!</code> in the browser.
![NX in the browser](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w78znd3eedx35xihs0ab.png)

#### Update <code>apps/api/src/main.ts</code> to serve the built <code>nx-fullstack</code> client once built

```
import * as express from 'express';
import * as path from 'path';
import { Message } from '@nx-fullstack/api-interfaces';

const CLIENT_BUILD_PATH = path.join(__dirname, '../nx-fullstack');

const app = express();
app.use(express.static(CLIENT_BUILD_PATH));

const greeting: Message = { message: 'Welcome to api!' };

app.get('/api', (req, res) => {
  res.send(greeting);
});

app.get('*', (request, response) => {
  response.sendFile(path.join(CLIENT_BUILD_PATH, 'index.html'));
});

const port = process.env.PORT || 3333;
const server = app.listen(port, () => {
  console.log('Listening at http://localhost:' + port + '/api');
});
server.on('error', console.error);
```

#### Update build script in package.json and commit it to git

```
"build": "yarn nx run-many --target=build --projects=nx-fullstack,api --parallel=true"
```

#### Update start script in package.json and commit it to git

```
"start": "node dist/apps/api/main.js"
```

## Deploy app to Heroku

Sign up for a [free account at Heroku here](https://signup.heroku.com/). Install the Heroku CLI by running the command below in the terminal.

```
brew tap heroku/brew && brew install heroku
```

Run the heroku login command

```
heroku login
```

Heroku will ask you to authenticate yourself in the browser.
![Heroku login](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/omasa83uvze5yvnzrapk.png)
Once it's complete you can return to the terminal. You're now logged in.

#### Create a Heroku deploy target

Run the CLI command for creating a new deploy target in your Heroku account.

```
heroku create
```

Once it's finished it will look like this.
![Heroku deploy target](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/i1ruo0w4v2j2r59z8x35.png)

#### Procfile in the root of your project

Create a <code>Procfile</code> in the root of your project and add the following

```
web: yarn start
```

#### Deploy code to Heroku

Make sure all your changes in the repo are commited. Then run

```
git push heroku master
```

#### Visit your deployed fullstack app

Use the CLI command below to open up your deployed app in your default browser.

```
heroku open
```

#### Voila!

Your fullstack NX Workspace app is [now deployed and running on Heroku](https://thawing-brushlands-09373.herokuapp.com/).
