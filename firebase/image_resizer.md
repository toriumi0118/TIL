# Create Image Resizer on Firebase

## Architecture

1. Upload files manually.
2. Return resized image via functions.

refs: https://github.com/firebase/functions-samples/tree/master/generate-thumbnail

## Build Up

1. create Storage with Firebase console.
1. install firebase dependencies to repository.
   ```bash
   $ yarn add firebase@latest
   $ npm install -g firebase-tools
   $ firebase login
   $ firebase init functions
   ```
   
