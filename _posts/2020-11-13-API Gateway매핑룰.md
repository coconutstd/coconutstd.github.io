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
    
    if response :
      return {
          'statusCode': 200,
          'body': items
      }
```

![image-20201113203428768](/assets/img/post/restapi1/1.png)



위 코드는 product_code를 키로 Dynamo DB를 조회하는 람다함수 입니다. 그리고, 테스트 이벤트를 구성하여 람다함수에 리퀘스트 요청이 들어왔을 때를 시뮬레이션 해본 결과입니다. 정상적으로 핸들러 event에 쿼리가 들어오는 것을 알 수 있습니다.

![image-20201113205731470](/assets/img/post/restapi1/2.png)





## API Gateway와 Lambda 함수 연결 및 해결



API Gateway에 리소스와 GET 메서드를 생성해주고 앞서 생성한 람다함수를 연결했습니다.

그리고 테스트에 쿼리 파라미터를 넣고 수행한 결과 다음과 같은 오류가 났습니다.

![image-20201113210328058](/assets/img/post/restapi1/3.png)



'product_code'를 쿼리 문자열에 넣어줬지만 람다함수에는 전달되지 않아서 생긴 오류입니다.

![image-20201113210445180](/assets/img/post/restapi1/4.png)

통합 요청 -> 매핑 템플릿 -> 정의된 템플릿이 없는 경우 -> application/json -> 매핑룰 작성

![image-20201113210658201](/assets/img/post/restapi1/5.png)

쿼리 스트링으로 들어온 product_code(우측)를 람다함수의 이벤트 객체에 product_code(좌측)으로 매핑 시켜줍니다. 그러면 다시 테스트를 수행했을 때 정상적으로 DB에 GET의 쿼리스트링으로 질의한 결과를 얻을 수 있습니다.

![image-20201113210936361](/assets/img/post/restapi1/6.png)



## 정리

REST API를 개발할 때, 람다함수를 생성하고 올바르게 작동하는지 테스트를 수행합니다. 하지만, API Gateway에 연결하여 API를 노출할 때 GET 요청 URL의 쿼리스트링이 전달되지 않았습니다. 이를 해결하기 위해서 API Gateway 측에서 람다함수랑 통합할 때 매핑룰을 작성해주면 해결됩니다.