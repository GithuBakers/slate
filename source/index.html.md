---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  # - shell
  # - ruby
  # - python
  # - javascript
  - json

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

# includes:
  # - errors

search: true
---

# 迭代二文档 (/api/v2)

# ALL

## 注册

**HTTP REQUEST**

`POST /user/new_user`

> BODY

```json
{
  "user_name":string,
  "role":string(worker, admin, initiator),
  "password":(加密后),
  "avatar":url,
  "nick_name":string
}
```

> RETURN

```json
true/false
```


## 登录

**HTTP REQUEST**

`POST /login`

> Body

```json
{
  "user_name":用户名,
  "password":密码
}
```

> Header

```json

"Authorization":"Bearer "+token,
"Roles":[role1,role2,...]

```

## 修改密码


**HTTP REQUEST**

`POST /user/password/{user_name}`

> BODY

```json
{
  "ori_password":加密后的,
  "new_password":加密后的新密码
}

```

> RETURN

```json
true/false
```


# 发起者

## 查看个人信息

**HTTP REQUEST**

`GET /initiator/information/{user_name}`

> RETURN

```json
{
  "user_name":string,
  "role":string(worker, admin, initiator),
  "avatar":url,
  "nick_name":string,

}

```



## 修改个人信息


**HTTP REQUEST**

`POST /initiator/information/{user_name}`

> BODY

```json
{
  "avatar":url,
  "nick_name":string
}

```

> RETURN

```json
true/false

```


## 新建任务

**HTTP REQUEST**

`POST /initiator/task/running_task`

> BODY

```json
{
  "task_id":string,
  "task_name":string,
  "initiator_name":string,  //有可能在未来版本消失
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "data_set":[
    {
      "id":图片id,
      "raw":url
    },
    ...
  ],
  "aim":int 目标标注人数/张,
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string, //任务要求
  "keywords":[  //标注的关键词
    string1,string2,...
  ],
  "dependencies":[  //任务依赖的标准集（标准集id）
    string1,string2,.....
  ]

}

```

> RETURN

```json
true/false
```

## 发起者查看自己的已发起任务列表

**HTTP REQUEST**

`GET /initiator/task`

**参数说明**

|参数名|参数意义|参数类型|
|-----|---|----|
|finished|是否已结束|bool|

> RETURN

```json
[
  {
    "task_id":string,
    "task_name":string,
    "cover":url,
    "type":(RECT,DESC,EDGE),
    "completeness":float,//达标比例
    "finished":bool//状态
  },
  ...
]

```


## 发起者查看自己发起的某一具体任务

**HTTP REQUEST**

`GET /initiator/task/{task_id}`

> RETURN

```json

{
  "task_id":string,
  "task_name":string,
  "initiator_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "aim":int 目标标注人数,
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string , //任务要求
  "total_reward":发出的总的reward,
  "completeness":float,//达标比例
  "result":url,//结果所在地
  "finished":bool,//状态
  "keywords":[  //标注的关键词
    string1,string2,...
  ],
  "dependencies":[  //任务依赖的标准集对象们
    {},{},...
  ]
}


```


## 发起者结束自己的某一任务

**HTTP REQUEST**

`POST /initiator/task/finished_task`

> BODY

```json
{
  "task_id":string
}
```

> RETURN

```json
{
  "task_id":string,
  "task_name":string,
  "initiator_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "aim":int 目标标注人数,
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string ,
  "total_reward":发出的总的reward,
  "completeness":float,
  "result":url,
  "keywords":[  //标注的关键词
    string1,string2,...
  ],
  "dependencies":[  //任务依赖的标准集对象们
    {},{},...
  ]
}
```



## 发起者创建标准

**新增功能**

`POST initiator/criterion/new_criterion`

```json
{
  "criterion_id":string,
  "criterion_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "data_set":[
    {
      "id":图片id,
      "url":url
    },
    ...
  ],
  "aim":int 用户通过的标准（至少完成多少张）,//张数必须小于等于上传图片总张数
  "requirement":string, //任务要求（用户该做什么）
  "keywords":[  //标注的关键词
    string1,string2,...
  ]

}

```

> RETURN

```json
true/false
```

## 发起者查看自己发起的所有标准


`GET /initiator/criterion/myself`


**参数说明**

|参数名|参数意义|参数类型|
|-----|---|----|
|initiator|发起者ID|string|

>RETURN

```json
[
  {
    //参见criterion
  }
]
```


## 发起者查看所有标准

`GET /initiator/criterion`

>RETURN

```json
[
  {
    //参见criterion
  }
]
```

## 发起者做自己的标准（获取图片列表）

`GET /initiator/criterion/img`

|参数名|参数意义|参数类型|
|-----|---|----|
|criterion_id|标准ID|string|

>RETURN

```json
[ 
  { 
  "id":图片ID,
  "name":(String)图片名,
  "raw":(String)原图的URL 
  }, 
  ...(若干个)
]
```

## 发起者做自己的标准

`POST /initiator/criterion/img`
|参数名|参数意义|参数类型|
|-----|---|----|
|criterion_id|标准ID|string|


>REQUEST

```json
{

  "id":图片id,
  "tags":
  [
    {
      "id":(String)标注的ID,
      "mark":{
        "type":（EDGE, DESC, RECT)
      },//某种类型的标记，**必须包含**类型字段
      "comment":备注
    },
    ...
  ]
}
```

<!-- # 回收任务

GET /initiator/task/finished_task/{task_name}

```json
{
  "result":url //数据结果的url，通常放在oss上
}
``` -->


#  工人


## 工人查看所有标准

`GET /worker/criterion`

>RETURN

```json
[
  {
    //参见criterion
  },...
]
```


## 工人做某一标准（获取图片列表）

`GET /worker/criterion/img`

|参数名|参数意义|参数类型|
|-----|---|----|
|criterion_id|标准ID|string|

>RETURN

```json
[ 
  { 
  "id":图片ID,
  "name":(String)图片名,
  "raw":(String)原图的URL 
  }, 
  ...(若干个)
]
```


## 工人做某一标准


`POST /worker/criterion/img`
|参数名|参数意义|参数类型|
|-----|---|----|
|criterion_id|标准ID|string|


>REQUEST

```json
{

  "id":图片id,
  "tags":
  [
    {
      "id":(String)标注的ID,
      "mark":{
        "type":（EDGE, DESC, RECT)
      },//某种类型的标记，**必须包含**类型字段
      "comment":备注
    },
    ...
  ]
}
```

>RETURN 

>返回的是正确答案

```json
{
  "correct":boolean,//是否正确
  "id":图片id,
  "tags":
  [
    {
      "id":(String)标注的ID,
      "mark":{
        "type":（EDGE, DESC, RECT)
      },//某种类型的标记，**必须包含**类型字段
      "comment":备注
    },
    ...
  ]
}
```



## 查看个人信息

**HTTP REQUEST**

`GET /worker/information/{user_name}`

> RETURN

```json
{
  "user_name":string,
  "role":string(worker, admin, initiator),
  "avatar":url,
  "nick_name":string,
  "credit":积分,
  "rank":用户的 排名？,
  "dependencies":[ 
    {},...//标准对象的列表
  ]
}

```


## 修改个人信息

**HTTP REQUEST**

`POST /worker/information/{user_name}`

> BODY

```json
{
  "avatar":url,
  "nick_name":string
}

```

> RETURN

```json
true/false

```

## 获取推荐任务

**HTTP REQUEST**

`GET /worker/recommend_task`

|参数名|参数含义|
|----|-------|
|type|任务类型(RECT,DESC,EDGE)|

> RETURN

```json
{
  "task_id":string,  
  "task_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string,
  "keywords":[  //标注的关键词
    string1,string2,...
  ] 
}

```



## 获取任务列表

**HTTP REQUEST**

`GET /worker/task_list`

> RETURN

```json
[
  {
    "task_id":string,
    "cover":任务封面,
    "type":任务类型,
    "task_name":任务名,
    "reward":奖励
  },
  ...
]

```

## 获取任务详情

**HTTP REQUEST**

`GET /worker/task/{task_id}`

> RETURN

```json
{
  "task_id":string,  
  "task_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string,
  "keywords":[  //标注的关键词
    string1,string2,...
  ],
  "dependencies":[  
    {
      "qualified":boolean,//用户是否完成了这一项
      //其余部分字段和criterion
    }
  ] 
}

```

## 接受任务

**HTTP REQUEST**

`POST /worker/task/received_task/{task_id}`

> RETURN

```json
true/false
```




## 获取任务图片列表

**HTTP REQUEST**

`GET /worker/task/received_task/img/{task_id}`

> RETURN

```json
[ 
  { 
  "id":图片ID,
  "name":(String)图片名,
  "raw":(String)原图的URL 
  }, 
  ...(若干个)
]
```


## 提交某一张标注

**HTTP REQUEST**

`POST /worker/task/received_task/{task_id}/{img_id}`

> BODY

```json
{

  "id":图片id,
  "tags":
  [
    {
      "id":(String)标注的ID,
      "mark":{
        "type":（EDGE, DESC, RECT)
      },//某种类型的标记，**必须包含**类型字段
      "comment":备注
    },
    ...
  ]
}
```

> RETURN

```json
true/false
```

## 查看已接受任务列表

**HTTP REQUEST**

`GET /worker/task/received_task`

> RETURN

```json
[
  {
    "task_id":string,
    "task_name":string,
    "cover":url,
    "type":(RECT,DESC,EDGE),
    "actual_number":现在已标注多少张了,
    "total_reward":获取的所有的奖励数,
    "finished":bool
    
  },
  ...
]

```


## 查看某一已接受任务

**HTTP REQUEST**

`GET /worker/task/received_task/{task_id}`

> RETURN

```json

{
  "task_id":string,  
  "task_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string,
  "actual_number":现在已标注多少张了,
  "keywords":[
    string1,string2,...
  ]
  "total_reward":获取的所有的奖励数
}


```










#  管理员

## 查看系统情况

**HTTP REQUEST**

`GET /admin/sys_info`

> RETURN

```json

{
  "initiator_number":int,
  "worker_number":int,
  "total_user_number":int,
  "unfinished_number":int,
  "finished_number":int,
  "total_task_number":int
}


```