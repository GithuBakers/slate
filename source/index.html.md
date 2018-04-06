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

> Header

```json
{
  "username":用户名,
  "password":密码
}
```

> RETURN

```json

"Authorization":"Bearer "+token,
"Roles":[role1,role2,...]

```

## 查看个人信息

**HTTP REQUEST**

`GET /user/information/{user_name}`

> RETURN

```json
{
  "user_name":string,
  "role":string(worker, admin, initiator),
  "avatar":url,
  "nick_name":string,
  "credict":积分,
  "rank":用户的 排名？
}

```

## 修改个人信息

**HTTP REQUEST**

`POST /user/information/{user_name}`

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

## 新建任务

**HTTP REQUEST**

`POST /initiator/task/running_task`

> BODY

```json
{
  "task_name":string,
  "initiator_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "data_set":[
    {
      "id":图片id,
      "url":url
    },
    ...
  ],
  "aim":int 目标标注人数/张,
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string //任务要求

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

`GET /initiator/task/{task_name}`

> RETURN

```json

{
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
  "finished":bool//状态

}


```


## 发起者结束自己的某一任务

**HTTP REQUEST**

`POST /initiator/task/finished_task`

> BODY

```json
{
  "task_name":string
}
```

> RETURN

```json
{
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
  "result":url

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

## 获取任务列表

**HTTP REQUEST**

`GET /worker/task_list`

> RETURN

```json
[
  {
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

`GET /worker/task/{task_name}`

> RETURN

```json
{
  "task_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string 
}

```

## 接受任务

**HTTP REQUEST**

`POST /worker/task/received_task/{task_name}`

> RETURN

```json
true/false
```




## 获取任务图片列表

**HTTP REQUEST**

`GET /worker/task/received_task/img/{task_name}`

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

`POST /worker/task/received_task/{task_name}/{img_id}`

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

`GET /worker/task/received_task/{task_name}`

> RETURN

```json

{
  "task_name":string,
  "cover":url,
  "type":(RECT,DESC,EDGE),
  "limit":int 单个用户最多标注的数量,
  "reward":int credit/10,
  "requirement":string,
  "actual_number":现在已标注多少张了,
  "total_reward":获取的所有的奖励数
}


```






