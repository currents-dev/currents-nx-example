# currents-nx-example

Example integration of NX using [`@currents/nx` plugin](https://www.npmjs.com/package/@currents/nx) for running cypress tests with Currents.dev.

## Walkthrough

`@currents/nx` uses `@nrwl/cypress` behind the scenes - you need to install and configure `@nrwl/cypress` following the [documentation](https://nx.dev/l/r/cypress/overview).

You can recreate the example following the next steps.

Setup your project.

```sh

# Create an "empty" workspace
npx create-nx-workspace@latest currents-nx-example
cd currents-nx-example

# Create a dummy web project, choose any CSS styling
# That will create a new project with `@nrwl/cypress` pre-installed and configured
npm i -D @nrwl/web
nx g @nrwl/web:app frontend

# Install @currents/nx
npm i -D @currents/nx

# Configure a new target in apps/frontend-e2e/project.json
vim apps/frontend-e2e/project.json
```

Set executor value to `"@currents/nx:currents"`, and `"options.cypressExecutor"` to the target name that's running `"@nrwl/cypress:cypress"` - in our case its `"e2e"`. See [example](https://github.com/currents-dev/currents-nx-example/blob/5cdf29cae42c2c8dfd6af52f80d9fa9f2ff886de/apps/frontend-e2e/project.json).

```json
"currents": {
  "executor": "@currents/nx:currents",
  "options": {
    "cypressExecutor": "e2e"
  }
}
```

Set `projectId` obtained from https://app.currents.dev in `cypress.config.js` file. Now you can start recording your tests to Currents.dev dashboard.

```sh
nx run frontend-e2e:currents --record --key <key> --ci-build-id hello-currents-nx -- --parallel
```

> Please note the use of `-- --parallel` flag due to conflicts with existing NX flags. See [#8014](https://github.com/nrwl/nx/issues/8014), [#1604](https://github.com/nrwl/nx/issues/1604).

You can also omit CLI flags and instead provide cypress options within `@nrwl/cypress:cypress` executor's `options`, for example:

```json
// ...
"currents": {
  "executor": "@currents/nx:currents",
  "options": {
    "cypressExecutor": "e2e"
  }
},
"e2e": {
  "executor": "@nrwl/cypress:cypress",
  "options": {
    "cypressConfig": "apps/frontend-e2e/cypress.json",
    "devServerTarget": "frontend:serve",
    "record": true,
    "parallel": true,
    "key": "currents key"
  },
  "configurations": {
    "production": {
      "devServerTarget": "frontend:serve:production"
    }
  }
}
// ...
```

While having those options defined, you can omit the corresponding CLI flags:

```sh
nx run frontend-e2e:currents  --ci-build-id hello-currents-nx-001
```

Here's a visual example of the demo workflow sending cypress test results to Currents dashboard

![Kapture 2021-11-19 at 01 14 50](https://user-images.githubusercontent.com/1637928/142597762-3cc0009f-d030-46aa-b273-1c31300c65f6.gif)
