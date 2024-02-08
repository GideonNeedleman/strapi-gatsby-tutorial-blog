# strapi-gatsby-tutorial-blog

Building a static blog in strapi and gatsby

Following tutorial on [Strapi blog](https://strapi.io/blog/how-to-build-a-static-blog-with-gatsby-and-strapi)

## Create Strapi Backend

`npx create-strapi-app@latest backend`
choose quickstart

## Create Gatsby Frontend

Follow instructions on blog post, but remember to add `import "./layout.css":` to layout.js file.

## Fake Deployment

Static website deployed to [cloudflare](https://ec26ae41.strapi-gatsby-blog-a5c.pages.dev/) using wrangler.

`npm install wrangler --save-dev`

`npx wrangler pages deploy public`

## Real Deploy Strapi Backend to Render

### Setup Cloudinary file storage

1. Create Cloudinary account
2. Install [Strapi Cloudinary plugin](https://market.strapi.io/providers/@strapi-provider-upload-cloudinary)

`npm install @strapi/provider-upload-cloudinary`

3. backend/config/plugins.js add code from blog post.
4. copy `CLOUDINARY_NAME, CLOUDINARY_KEY CLOUDINARY_SECRET` variables into .env file
5. Add values from Cloudinary account dashboard

**Optional:** You can create a folder inside Cloudinary to hold media for this site. Then in plugins.js change empty `uploadStream: {}` to:

```js
uploadStream: {
  folder: env("CLOUDINARY_FOLDER"),
},
```

and add `CLOUDINARY_FOLDER` variable to .env file with value = folder name in Cloudinary.

6. backend/config/middlewares.js replace `"strapi::security"` with code from blog post

### Deploy to Render.com

1. Create account at Render
2. New web service from Git repo
3. Root directory: backend
4. Build command: `npm install && npm run build`
5. Start command: `npm run start`
6. Add .env file under advanced

### Setup Neon.tech postgres database

**Warning:** Switching to a postgres database will avoid using any data you may have entered into sqlite db. Will also override connection to assets in Cloudinary.

Options:

- Connect to neon db before adding any content at all. That way no migration is needed and all content is stored in neon db from the start.
- Make a sqlite branch of Strapi project to maintain local sqlite db of content. On main branch install db, so deployed strapi rebuilds with postgres db. Then migrate data from local sqlite branch to deployed main branch as described in [next section](#transfer).

1. Create account at [Neon](neon.tech)
2. Install postgres `npm i pg`
3. Install [Strapi neon tech db plugin](https://market.strapi.io/plugins/strapi-neon-tech-db-branches)

`npm i strapi-neon-tech-db-branches`

<!-- Nevermind, don't need to do this if you add env vars

3. backend/config/plugins.js add:

```js
"strapi-neon-tech-db-branches": {
    enabled: true,
    config: {
      neonApiKey: env("NEON_API_KEY"), // get it from here: https://console.neon.tech/app/settings/api-keys
      neonProjectName: env("NEON_PROJECT_NAME"), // the neon project under wich your DB runs
      neonRole: env("NEON_ROLE"), // create it manually under roles for your project first
      gitBranch: env("GIT_BRANCH"), // branch can be pinned via this config option. Will not use branch from git then. Usefull for preview/production deployment
    },
  },
``` -->

4. Generate Neon api key from above link and add variables to .env file: `NEON_API_KEY, NEON_PROJECT_NAME, NEON_ROLE, GIT_BRANCH` - only add env vars to production deployment to prevent losing local dev content data.
5. Remember in development to change value of GIT_BRANCH to dev branch to avoid committing data to production branch db.

### <span id="transfer"/> Transfer local dev data to remote production

You can do this if you don't switch to postgres in local dev environment. Then your data will be intact and you can push data to remote production that is running postgres. The way to do this is to create a branch that maintains sqlite database.

Follow [Youtube tutorial here](https://youtu.be/RlAv2RNbQjE). And [blog post here.](https://docs.strapi.io/dev-docs/data-management/transfer)

Will need to create 'push' api token in strapi

## Gatsby deploy

Consider editing gatsby-config.js to only accept `.env` file:

```js
require("dotenv").config({
  path: `.env`,
});
```
