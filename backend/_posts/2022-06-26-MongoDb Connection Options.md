---
layout: post
title: MongoDB Connection Options
description: >
    몽고 디비의 가상 스키마
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 개요

```javascript
const mongoose = require("mongoose");
const schema = require("./schema");

const db = mongoose.connection;
const model = (()=> {
    db.on("error",console.error);
    db.on("open",()=> {
        console.log("Connecting mongodb!");
    });
    // 몽고디비 웹 액세스 주소
    mongoose.connect(
        `mongodb+srv://miewone:아니@cluster0.e5ipp.mongodb.net/?retryWrites=true&w=majority`,
        // { useNewUrlParser:true, useUnifiedTopology:true,}
    );
    //  스키마 연결

})();

module.exports=model;
```