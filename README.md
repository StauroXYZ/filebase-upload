# filebase-upload

[![Version][v-badge-url]][npm-url] [![GitHub Workflow Status][gh-actions-img]][github-actions]
[![Codecov][cov-badge]][cov] [![][docs-badge]][docs]

Minimal library to upload files on [Filebase](https://filebase.com) S3 API. Partially based on
[AWS SDK v3](https://github.com/aws/aws-sdk-js-v3).

## Install

```sh
pnpm i @stauro/filebase-upload
```

## Usage

First, you need to set up a token. A token is a base64 encoded pair of the access key and access secret.

You can generate a token like this:

```sh
echo "accessKey:accessSecret" | base64
```

and save it to a `.env` file or somewhere else.

### Node.js

```ts
import { createPresignedUrl } from '@stauro/filebase-upload'

const file = new File(['Hello world'], 'hello.txt')

const url = await createPresignedUrl({
  bucketName: `example-${crypto.randomUUID()}`,
  token: process.env.FILEBASE_TOKEN,
  file,
  apiUrl: 's3.filebase.com',
})

await fetch(decodeURIComponent(url), { method: 'PUT', body: file })
```

And then run as:

```sh
node --env-file=.env main.js
```

### Deno

```ts
import { createPresignedUrl } from 'https://deno.land/x/filebase_upload/mod.ts'
import { load } from 'https://deno.land/std@0.207.0/dotenv/mod.ts'

const env = await load()

const file = new File(['Hello world'], 'hello.txt')

const url = await createPresignedUrl({
  bucketName: `example-${crypto.randomUUID()}`,
  token: env.FILEBASE_TOKEN,
  file,
  apiUrl: 's3.filebase.com',
})

await fetch(decodeURIComponent(url), { method: 'PUT', body: file })
```

And then run as:

```sh
deno --allow-read --allow-net mod.ts
```

## API

### createPresignedUrl

Creates a presigned URL for file upload. All options are required.

### getObject

Retrieves an object from Filebase S3 API and returns a response object.

You can also retrieve the file CID from headers.

```ts
import { getObject } from 'https://deno.land/x/filebase_upload/mod.ts'

const res = await getObject({
  bucketName: `example-${crypto.randomUUID()}`,
  token: env.FILEBASE_TOKEN,
  apiUrl: 's3.filebase.com',
  filename: 'hello.txt',
})

console.log(`${res.headers.get('x-amz-meta-cid')}:`, await res.text())
```

### headObject

Checks if the file has been uploaded. Returns a boolean and a CID of the file (if uploaded).

```ts
import { headObject } from 'https://deno.land/x/filebase_upload/mod.ts'

const [isUploaded, cid] = await headObject({
  bucketName: `example-${crypto.randomUUID()}`,
  token: env.FILEBASE_TOKEN,
  apiUrl: 's3.filebase.com',
  filename: 'hello.txt',
})

console.log(`is uploaded? ${isUploaded ? 'yes' : 'no'}`)
```

[docs-badge]: https://img.shields.io/github/v/release/staurodev/filebase-upload?label=Docs&logo=deno&style=for-the-badge&color=FFAE00
[docs]: https://doc.deno.land/https/deno.land/x/filebase_upload/mod.ts
[gh-actions-img]: https://img.shields.io/github/actions/workflow/status/staurodev/filebase-upload/ci.yml?branch=master&style=for-the-badge&logo=github&label=&color=FFAE00&
[github-actions]: https://github.com/staurodev/filebase-upload/actions
[cov]: https://coveralls.io/github/StauroDEV/filebase-upload
[cov-badge]: https://img.shields.io/coveralls/github/StauroDEV/filebase-upload?style=for-the-badge&color=FFAE00
[v-badge-url]: https://img.shields.io/npm/v/@stauro/filebase-upload?style=for-the-badge&logo=npm&label=&color=FFAE00
[npm-url]: https://www.npmjs.com/package/@stauro/filebase-upload
