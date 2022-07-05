---
layout: post
title: MongoDB virtuals
description: >
    몽고 디비의 가상 스키마
sitemap: true
hide_last_modified: false
---

1. 개요
{:toc}

# 개요

강의를 듣는데 아래와 같이 가상이라는 함수 호출하는것을 봤습니다. 

```javascript
// User는 Mongoose의 Schema 객체 타입입니다.
User.virtual("password").set((password)=>{
    this._password= password;
    this.salt = this.makeSalt();
    this.hashedPassword = this.encryptPassword(password);
});
```

[몽구스js](https://mongoosejs.com/docs/tutorials/virtuals.html)에서 말하기로는

>Mongoose에서 가상은 MongoDB에 저장되지 않는 속성입니다. 가상은 일반적으로 문서의 계산된 속성에 사용됩니다.

```javascript
const userSchema = mongoose.Schema({
  email: String
});
// Create a virtual property `domain` that's computed from `email`.
userSchema.virtual('domain').get(function() {
  return this.email.slice(this.email.indexOf('@') + 1);
});
const User = mongoose.model('User', userSchema);

let doc = await User.create({ email: 'test@gmail.com' });
// `domain` is now a property on User documents.
doc.domain; // 'gmail.com'
```

JAVA나 C# 언어 처럼 get,set 명령어를 이용하여 값을 설정하고 값을 가져올 수 있게 조작이 가능합니다.

위 코드를 보면 mongoose 스키마로 된 userSchema가 존재합니다 이 userSchema에 가상 속성 `domain`(get)을 만들어
`domain`을 호출하면  모델 User에 email 값인 `gmail.com`이 반환됩니다.

이 가상 속성을 Mongoose가 JSON으로 반환할때 기본적으로는 포함이 안되지만 `toJSON({ virtuals: true })` 속서을 추가하면
가상 속성도 같이 반환이 됩니다. 

 