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

### Setup Neon.tech postgres database

1. Create account at [Neon](neon.tech)
2. Install [Strapi neon tech db plugin](https://market.strapi.io/plugins/strapi-neon-tech-db-branches)

`npm i strapi-neon-tech-db-branches`

### Transfer local dev data to remote production
