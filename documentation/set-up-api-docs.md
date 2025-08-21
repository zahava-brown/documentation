## Update API reference docs

While anyone in the community can propose changes to our API reference docs, the relevant JSON files are built from code in F5 closed source repositories.

Therefore, the audience for this document is limited to F5/NGINX employees. 

At this time, the only published and supported API reference docs are available for NGINX One Console, at https://docs.nginx.com/nginx-one/api/api-reference-guide/.

To set up API reference docs for NGINX One Console, follow these steps:

1. Clone the documentation repository, https://github.com/nginx/documentation. 
1. Set up Hugo on your system, as described in [Managing content with Hugo](/documentation/writing-hugo.md).
1. Go to the apprpriate internal repository. Create the `one.json` file with the changes that you need.
   - Make sure the changes on that `one.json` file are limited to those endpoints that are ready for the public.
1. Set up a branch on your clone of the documentation repository.
1. Copy your one.json file to that branch
   - If you're updating the API for NGINX One Console, copy it to the following subdirectory: [static/nginx-one/api/](https://github.com/nginx/documentation/tree/feat-api-self-serve-process/static/nginx-one/api).
1. Verify the changes on your local system. 
   - Build the changes with the `make watch` command as described in [Managing content with Hugo](/documentation/writing-hugo.md).
   - Review the result on your system at http://localhost:1313
   - For the NGINX One API, the full URL is http://localhost:1313/nginx-one/api/api-reference-guide/
1. To publish this change to the production NGINX documentation repository, push the changes from your branch. 
1. Start a pull request on the documentation repository, https://github.com/nginx/documentation.
1. Once the pull request is approved and merged, your new API reference is published.
