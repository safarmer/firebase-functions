# firebase-functions

A simple MCVE to show firebase functions not hot reloading on Linux.

## Steps to reprroduce (assuming firebase-tools is already setup):

```bash
git clone https://github.com/safarmer/firebase-functions.git
yarn
yarn build --watch

# In a new terminal
firebase serve --only functions
```

You should now be able to open the URL for the helloworld HTTPS function. It should say `Hello from TypeScript!`. On macOS, it should be possible to edit the file `functions/src/handlers/helloworld.ts` and see the text update without restarting the server. On Linux, the only way to get the text to change is to restart the server.

## Workaround for local development

Update `firebase.json` to the following and restart the server:

```json
{
  "functions": {
    "predeploy": [
      "npm --prefix \"$RESOURCE_DIR\" run lint",
      "npm --prefix \"$RESOURCE_DIR\" run build"
    ],
    "source": "functions/lib"
  }
}
```

It is now possible to have the code hot reload by modifying the file `functions/lib/index.js`. To further spead up local development, you can use Webpack to output a single file in `functions/lib/index.js`.

The problem with this workaround is that `firebase deploy` no longer works as the `package.json` file is not in `functions/lib`.
