# Formidable-Memory

[![Build Status](https://travis-ci.org/crafter999/node-formidable.svg?branch=master)](https://travis-ci.org/crafter999/node-formidable)

This repo was forked from https://github.com/felixge/node-formidable to handle
multipart/form-data (file uploads) on serverless aplications like Webtask.io or AWS Lambda.
It just save the data to a buffer instead of a file. It's a quick & dirty hack-patch and
I don't recommended for production envs unless you know what you're doing.
PS. maxFileSize & maxFieldsSize were set to 2MB.
## Installation

```sh
npm install formidable-memory
```

## Example
```js
const http = require("http");
const formidable = require("./node-formidable");

const server = http.createServer((req, res) =>{
   let form = new formidable.IncomingForm({memoryStore: true});
   form
      .on('file', function (name, contents){
         console.log("I just received this file: "+name);
      })
      .on('end', function (){
         for (let x = 0; x<this.files.length; x++){
            console.log("name:"+this.files[x].name);
            console.log("contents"+this.files[x].contents.toString("utf-8"))
         }
      })
      .on('error', function (e){
         if (e.message.includes("maxFileSize exceeded"))
            console.log("rejected file from user due to oversized");
         else
            console.error(e)
      });
   form.parse(req);
   res.end();
});
server.on('clientError', (err, socket) =>{
   socket.end('HTTP/1.1 400 Bad Request\r\n\r\n');
});
server.listen(8080);
```