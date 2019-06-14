# Create Image Resizer with Cloud Functions.

## Architecture

1. Upload files manually.
2. Return resized image via functions.

refs: https://github.com/firebase/functions-samples/tree/master/generate-thumbnail

## Build Up

1. create Storage with Firebase console.
1. setup [gcloud sdk](https://cloud.google.com/sdk/docs/).
1. make directory structure.
   ```
   functions/
     - cmd/
       - main.go <= for development.
     - function.go <= run in gcp as entrypoint.
     - go.mod

## Source


