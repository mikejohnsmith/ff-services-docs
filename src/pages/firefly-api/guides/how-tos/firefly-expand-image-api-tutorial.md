---
title: Firefly Expand Image API Tutorial - Adobe Firefly API
description: Learn to become an advanced user of Firefly's Expand Image API. Generate multiple variations of an image tailored for various social media platforms.
keywords:
  - Adobe Firefly Services
  - Adobe Firefly Expand Image API
  - Adobe Firefly Expand Image API tutorial
  - Firefly API
  - Firefly Generative Expand
  - Expand Images
  - How-to guides
  - Firefly endpoint
contributors:
  - https://github.com/bishoysefin
  - https://github.com/mikemajzoub
hideBreadcrumbNav: true
---

# Firefly Expand Image API Tutorial

Create multiple variations of an image by generating larger sizes of the image with the [Expand Image API](../api/generative_expand/V3/).

![shoes advertisement hero image](../images/expand-image-tutorial-hero-image.png)

## Overview

For this tutorial, imagine we are working on a marketing campaign at a Fortune 100 company, and we need to create different sized images variations optimized for various social media platforms. In the past, we had to manually resize images for each platform, which was time-consuming and error-prone. With Firefly's Expand Image API, we automate this process and generate multiple variations of an images with dimensions optimized for platforms like Instagram, Facebook, Twitter, and more.

Before diving into the code, let's preview the high-level steps:

1. **Define Target Dimensions:** Each social media platform has specific image size requirements. We define these dimensions for the platforms you target.
2. **Upload Source Image:** Use Firefly's [Upload API](../api/upload_image/) to upload your original image.
3. **Generate Variations:** Use Firefly's [Expand Image API](../api/generative_expand/V3/) to create resized variations of the image for each dimension.

Depending on your learning style, you may prefer to walk through this tutorial step-by-step or [go straight to the full source code](#full-source-code).

## Prerequisites

This tutorial assumes you worked with your Adobe Representative and have the following: 

* An [Adobe Developer Console](https://developer.adobe.com/console/786177/home) account.
* A [Adobe Developer Console project](https://developer.adobe.com/developer-console/docs/guides/projects/projects-empty/) with Firefly API [OAuth Server-to-Server credentials set up](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-s2s/).
* Access to your Client ID and Client Secret from the [Adobe Developer Console project](https://developer.adobe.com/developer-console/docs/guides/services/services-add-api-oauth-s2s/#api-overview). Securely store these credentials and never expose them in client-side or public code.

Before we begin this tutorial, if you don't already have it, set up [Node.js](https://nodejs.org/en/download/package-manager). Run the following in a secure terminal:

```bash
export FIREFLY_CLIENT_ID=yourClientIdAsdf123
export FIREFLY_CLIENT_SECRET=yourClientSecretAsdf123

mkdir firefly-expand-image-api-tutorial
cd firefly-expand-image-api-tutorial
npm init --y
npm install axios qs
touch index.js
```

## Download Sample Image

Save this sample image to your project folder:

![expand-image-tutorial-source-image.webp](../images/expand-image-tutorial-source-image.png)

## Define Dimensions

First, let's define the image dimensions for each social media platform:

```js
const SOCIAL_MEDIA_PLATFORMS = [
  {
    name: 'Instagram Post',
    width: 1080,
    height: 1080,
  },
  {
    name: 'Facebook Cover',
    width: 820,
    height: 312,
  },
  {
    name: 'Twitter Post',
    width: 1024,
    height: 512,
  }
];
```

## Upload Source Image

 Next, let's upload our source image using Firefly's [Upload API](../api/upload_image/). This image serves as the starting point for all variations.

```js
const axios = require("axios");
const fs = require('fs');

async function uploadImage({ filePath, fileType, accessToken }) {
  let stream = fs.createReadStream(filePath);
  let stats = fs.statSync(filePath);
  let fileSizeInBytes = stats.size;

  const config = {
    method: 'post',
    url: 'https://firefly-api.adobe.io/v2/storage/image',
    headers: {
      'Authorization': `Bearer ${accessToken}`,
      'X-API-Key': process.env.FIREFLY_CLIENT_ID,
      'Content-Type': fileType,
      'Content-Length': fileSizeInBytes,
    },
    data: fileStream,
    maxContentLength: Infinity,
    maxBodyLength: Infinity,
  };

  const response = await axios(config);
  return response.data;
}
```

On success, the response looks similar to this:

```json
{
  "images":
    [
      {
        "id": "510aabb4-e154-4a7b-bd2e-f492ee71c938" 
    }
  ]
}
```

## Generate Image Variations

Next, let's create a function that generates images for each social media platform with Firefly's [Expand Image API](../api/generative_expand/V3/). We use the `axios` library to make HTTPs requests:

```js
const axios = require('axios');

async function genExpand({ imageId, width, height, accessToken }) {
  const body = {
    size: { width, height },
    image: { 
      source: { uploadId: imageId }
    }
  }

  const config = {
    method: 'post',
    url: 'https://firefly-api.adobe.io/v3/images/expand',
    headers: {
      'X-Api-Key': process.env.FIREFLY_CLIENT_ID,
      'Authorization': `Bearer ${accessToken}`,
      'Content-Type': 'application/json',
    },
    data: JSON.stringify(body),
  };

  const response = await axios(config);
  return response.data;
}
```

We can expand the image to fit each social media platform's optimized dimensions and log the URL of the generated image:

```js
async function createSocialMediaRenditions(accessToken) {
  // Upload the source image
  let upload = await uploadImage('./expand-image-tutorial-source-image.webp', 'image/webp', accessToken);
  let sourceImageId = upload.images[0].id;

  // Iterate over each platform and generate renditions
  for (let platform of SOCIAL_MEDIA_PLATFORMS) {
    let result = await genExpand(
      sourceImageId,
      platform.width,
      platform.height,
      accessToken
    );

    console.log(`Generated image variation for ${platform.name} at ${result.outputs[0].image.url}`);
  }
}
```
Once we know Firefly generated our images, we log the results, including the URL for each generated images.

## Full Example

You can review the [Prequisites](#prerequisites) section to understand how to set up your environment prior to running this code. Note that this is an example only ad is not production-ready and requires additional error handling, logging, security measures, and more before you can run it at scale in a live application.

```js
const axios = require("axios");
const qs = require("qs");
const fs = require("fs");

// Define social media platforms and their image dimensions
const SOCIAL_MEDIA_PLATFORMS = [
  {
    name: "Instagram Post",
    width: 1080,
    height: 1080,
  },
  {
    name: "Facebook Cover",
    width: 820,
    height: 312,
  },
  {
    name: "Twitter Post",
    width: 1024,
    height: 512,
  },
];

(async () => {
  const accessToken = await retrieveAccessToken();
  await createSocialMediaRenditions(accessToken);
})();

async function retrieveAccessToken() {
  let data = qs.stringify({
    grant_type: "client_credentials",
    client_id: process.env.FIREFLY_CLIENT_ID,
    client_secret: process.env.FIREFLY_CLIENT_SECRET,
    scope:
      "openid,AdobeID,session,additional_info,read_organizations,firefly_api,ff_apis",
  });

  let config = {
    method: "post",
    maxBodyLength: Infinity,
    url: "https://ims-na1.adobelogin.com/ims/token/v3",
    headers: {
      "Content-Type": "application/x-www-form-urlencoded",
    },
    data: data,
  };

  try {
    const response = await axios.request(config);
    const { access_token } = response.data;
    return access_token;
  } catch (error) {
    console.log(error);
  }
}

async function uploadImage({ filePath, fileType, accessToken }) {
  const fileStream = fs.createReadStream(filePath);
  const fileStats = fs.statSync(filePath);
  const fileSizeInBytes = fileStats.size;

  const config = {
    method: "post",
    url: "https://firefly-api.adobe.io/v2/storage/image",
    headers: {
      Authorization: `Bearer ${accessToken}`,
      "X-API-Key": process.env.FIREFLY_CLIENT_ID,
      "Content-Type": fileType,
      "Content-Length": fileSizeInBytes,
    },
    data: fileStream,
    maxContentLength: Infinity,
    maxBodyLength: Infinity,
  };

  const response = await axios(config);
  return response.data;
}

async function genExpand({ imageId, width, height, accessToken }) {
  const body = {
    numVariations: 1,
    size: {
      width,
      height,
    },
    image: {
      source: {
        uploadId: imageId,
      },
    },
  };

  const config = {
    method: "post",
    url: "https://firefly-api.adobe.io/v3/images/expand",
    headers: {
      "X-Api-Key": process.env.FIREFLY_CLIENT_ID,
      Authorization: `Bearer ${accessToken}`,
      "Content-Type": "application/json",
    },
    data: body,
  };

  const response = await axios(config);
  return response.data;
}

async function createSocialMediaRenditions(accessToken) {
  // Upload the source image
  let upload = await uploadImage({
    filePath: "./source-image.webp",
    fileType: "image/webp",
    accessToken,
  });

  let sourceImageId = upload.images[0].id;

  // Loop over each platform and generate renditions
  for (const platform of SOCIAL_MEDIA_PLATFORMS) {
    let result = await genExpand({
      imageId: sourceImageId,
      width: platform.width,
      height: platform.height,
      accessToken,
    });

    console.log(
      `Generated rendition for ${platform.name} at ${result.outputs[0].image.url}`,
    );
  }
}
```

This tutorial uses the CommmonJS convention in order to make it easy to get up and running with the code. If you'd prefer the ES6 modules, you can easily convert the code by changing the `require` statements to `import` statements and then changing the file name from `index.js` to `index.mjs`.

## Learn More

Now that you completed this tutorial, visit its [API Reference](../api/generative_expand/V3) to explore more advanced use cases to automate image generation with Firefly.