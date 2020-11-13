---
layout: post
title:  "API Gateway에서 람다로 쿼리스트링 넘겨주기"
summary: API Gateway 매핑룰 설정방법
author: coconut
date: 2020-11-13 8:30 PM
categories: [cloud]
tags: [API Gateway, lambda]
---
## API Gateway에서 람다함수로 쿼리스트링 전달하기

```python
import boto3
import json
from boto3.dynamodb.conditions import Key, Attr

def lambda_handler(event, context):
  
    ...
    
    response = table.query(
        KeyConditionExpression=Key('product_code').eq(event["product_code"])
    )
    
    ...
    
    return {
        'statusCode': 200,
        'body': items
    }
```

