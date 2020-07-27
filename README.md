# Fintech React app setup

This is a step-by-step guide to customize CRA for Fintech projects.

You will get an application which has;

- Sass
- Linting
- Formatting
- Testing

## Table of Contents

- [Step 1: Creating a new app](#step-1-creating-a-new-app)
- [Step 2: Removing CRA example files](#step-2-removing-cra-example-files)
- [Step 3: Installing Prettier](#step-4-installing-prettier)
- [Step 4: Installing ESLint](#step-5-installing-eslint)
- [Step 5: Enabling Sass](#step-6-enabling-sass)
- [Step 6: Installing stylelint](#step-7-installing-stylelint)
- [Step 7: Setting up our test environment](#step-8-setting-up-our-test-environment)
- [Step 8: Enabling hot reloading](#step-9-enabling-hot-reloading)
- [Step 9: Organizing Folder Structure](#step-10-organizing-folder-structure)
- [Step 10: Adding React Router](#step-12-adding-react-router)
- [Step 11: Enabling code-splitting](#step-13-enabling-code-splitting)
- [Step 12 Final Touches](#step-17-final-touches)

## Step 1: Creating a new app

First of all, we need to initialize our codebase via CRA command.

```sh
npx create-react-app cra-starter --template typescript
cd cra-starter
npm start
```

## Step 2: Removing CRA example files

In order to create our stack, we need to remove unnecessary CRA files.

## Step 4: Installing Prettier

We want to format our code automatically. So, we need to install Prettier.

```sh
npm install prettier --dev
```

`.prettierrc`

```jsonc
{
  "printWidth": 100,
  "singleQuote": true,
  "trailingComma": "all"
}
```

`.prettierignore`

```ignore
build
```

Also, we want to enable format on save on VSCode.

`.vscode/settings.json`

```json
{
  "editor.formatOnSave": true
}
```

Finally, we update `package.json` with related format scripts.

```json
"format:js": "prettier --write 'src/**/*.{js,jsx}'",
"format": "npm run format:js",
"format:check": "prettier -c 'src/**/*.{js,jsx}'"
```

## Step 5: Installing ESLint

We want to have consistency in our codebase and also want to catch mistakes. So, we need to install ESLint.

```sh
npm install eslint  eslint-plugin-react --dev
```

`.eslintrc`

```json
{
  "env": {
    "browser": true,
    "es6": true
  },
  "extends": ["plugin:react/recommended", "eslint:recommended"],
  "globals": {
    "Atomics": "readonly",
    "SharedArrayBuffer": "readonly"
  },
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": 2018,
    "sourceType": "module"
  },
  "plugins": ["react"],
  "rules": {
    "semi": [2, "always"]
  }
}
```

`.eslintignore`

```ignore
public
build
react-app-env.d.js
```

`.vscode/settings.json`

```json
{
  "eslint.validate": ["javascript", "javascriptreact"]
}
```

We need to update `package.json` for ESLint scripts.

```json
"lint:fix": "eslint 'src/**/*.{js,jsx}' --fix",
"format:js": "prettier --write 'src/**/*.{js,jsx}' && npm lint:fix"
```

## Step 6: Enabling Sass

CRA comes with Sass support out of the box. In order to enable it, we only add `node-sass` to our project.

```sh
npm install node-sass --dev
```

## Step 7: Installing stylelint

We also want a linter for our sass files. We need to install `stylelint`.

```sh
npm install stylelint stylelint-scss stylelint-config-recommended-scss --save-dev
```

`.stylelintrc`

```jsonc
{
  "extends": "stylelint-config-recommended-scss"
}
```

Finally, we need to update `package.json` and `.vscode/settings.json`

`package.json`

```json
"lint:css": "stylelint --syntax scss \"src/**/*.scss\"",
"lint": "npm run lint:ts && npm run lint:css",
"format:css": "stylelint --fix --syntax scss \"src/**/*.scss\"",
"format": "npm run format:js && npm run format:css"
```

## Step 8: Setting up our test environment

We'll use `jest` with `enzyme`.

```sh
yarn add enzyme enzyme-adapter-react-16 react-test-renderer --dev
yarn add @types/enzyme @types/enzyme-adapter-react-16 --dev
```

Also we need to install `enzyme-to-json` for simpler snapshosts.

```sh
yarn add enzyme-to-json --dev
```

We update our `package.json` for jest configuration.

```json
"scripts": {
  "coverage": "yarn run test --coverage"
},
"jest": {
  "snapshotSerializers": [
    "enzyme-to-json/serializer"
  ],
  "collectCoverageFrom": [
    "src/**/*.{js,jsx}",
    "!src/index.jsx",
    "!src/setupTests.js",
    "!src/components/**/index.{js,jsx}",
  ],
  "coverageThreshold": {
    "global": {
      "branches": 80,
      "functions": 80,
      "lines": 80,
      "statements": 80
    }
  }
}
```

And finally, we need to add the `coverage` to `.gitignore` and `.prettierignore`.

`.eslintignore`

```ignore
# ...
coverage
```

`.prettierignore`

```ignore
# ...
coverage
```

Before the testing, we need to add our setup file to initialize enzyme.

`src/setupTests.ts`

```ts
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
```

With this config, we are able to run tests with snapshots and create coverage. Let's add a simple test to verify our setup.

`src/App.test.js`

```js
import React from 'react';
import { shallow } from 'enzyme';
import App from './App';

it('runs correctly', () => {
  const wrapper = shallow(<App />);

  expect(wrapper).toMatchSnapshot();
});
```

Also, verify coverage report with `npm run coverage`.

## Step 9: Enabling hot reloading

We want to take advantage of hot reloading and don't want to lose React's current state. In order to do that we can use react hot loader.

Also we need to update hot reloader config.

`src/index.js`

```js
if (process.env.NODE_ENV !== 'production' && module.hot) {
  module.hot.accept();
}
```

## Step 10: Organizing Folder Structure

Our folder structure should look like this;

```
src/
├── App.test.jsx
├── App.jsx
├── __snapshots__
│   └── App.test.jsx.snap
├── components
│   └── Button
│       ├── Button.scss
│       ├── Button.test.js
│       ├── Button.js
│       ├── __snapshots__
│       │   └── Button.test.js.snap
│       └── index.ts
├── containers
│   └── Like
│       ├── Like.js
│       └── index.js
├── fonts
├── img
├── index.js
├── react-app-env.d.ts
├── routes
│   ├── Feed
│   │   ├── Feed.scss
│   │   ├── Feed.test.js
│   │   ├── Feed.js
│   │   ├── index.js
│   │   └── tabs
│   │       ├── Discover
│   │       │   ├── Discover.scss
│   │       │   ├── Discover.test.js
│   │       │   ├── Discover.js
│   │       │   └── index.js
│   │       └── MostLiked
│   │           ├── MostLiked.test.js
│   │           ├── MostLiked.js
│   │           └── index.js
│   ├── Home
│   │   ├── Home.scss
│   │   ├── Home.test.js
│   │   ├── Home.js
│   │   └── index.js
│   └── index.js
├── setupTests.js
├── styles
│   └── index.scss
└── utils
    ├── location.test.js
    └── location.js
```

## Step 11: Adding React Router

As usual, we want to use `react-router` for routing.

```
yarn add react-router-dom
yarn add @types/react-router-dom --dev
```

Then, we need to encapsulate our root component with `BrowserRouter`.

```tsx
// src/index.js

import React, { FunctionComponent } from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';

import App from './App';

const Root: FunctionComponent = () => (
  <BrowserRouter>
    <App />
  </BrowserRouter>
);

ReactDOM.render(<Root />, document.getElementById('root'));
```

```js
// src/routes/Routes.js

import React, { FunctionComponent } from 'react';
import { Switch, Route } from 'react-router-dom';

import Home from './Home';
import Feed from './Feed';

const Routes: FunctionComponent = () => (
  <Switch>
    <Route path="/" exact component={Home} />
    <Route path="/feed" exact component={Feed} />
  </Switch>
);

export default Routes;
```

```js
// src/App.js

import React, { FunctionComponent, Fragment } from 'react';

import Routes from './routes';

const App: FunctionComponent = () => (
  <Fragment>
    <header>Header</header>
    <Routes />
    <footer>Footer</footer>
  </Fragment>
);
```

## Step 13: Enabling code-splitting

We want to make route based code-splitting in order to prevent a huge bundled asset. When we done with this, only relevant assets will be loaded by our application. Let's install `react-loadable`.

```
yarn add react-loadable
yarn add @types/react-loadable --dev
```

Now, let's convert our routes to dynamically loaded.

```tsx
// src/routes/Home/index.js

import Loadable from 'react-loadable';

import Loading from '../../components/Loading';

const LoadableHome = Loadable({
  loader: () => import('./Home'),
  loading: Loading,
});

export default LoadableHome;
```

```js
// src/routes/Feed/index.js

import Loadable from 'react-loadable';

import Loading from '../../components/Loading';

const LoadableFeed = Loadable({
  loader: () => import('./Feed'),
  loading: Loading,
});

export default LoadableFeed;
```

## Step 13 Final Touches

We are ready to develop our application. Just a final step, we need to update our `README.md` to explain what we add a script so far.

```md
## Available Scripts

In the project directory, you can run:

### `npm run start`

Runs the app in the development mode.<br>
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will hot reload if you make edits.<br>
You will also see any lint errors in the console.

### `npm run test`

Launches the test runner in the interactive watch mode.<br>
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run coverage`

Launches coverage reporter. You can view the details from `coverage` folder.

### `npm run lint`

Runs ESLint and StyleLint. If any lint errors found, then it exits with code 1.

### `npm run format`

Formats js and Sass files.

### `npm run:check`

Checkes if any formatting error has been made.

### `npm run build`

Builds the app for production to the `build` folder.<br>
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.<br>
Your app is ready to be deployed!

## Learn More

You can learn more in the [Create React App documentation](https://facebook.github.io/create-react-app/docs/getting-started).

To learn React, check out the [React documentation](https://reactjs.org/).
```

## Step 18: Starting to Development 🎉

Everything is done! You can start to develop your next awesome React application now on 🚀
