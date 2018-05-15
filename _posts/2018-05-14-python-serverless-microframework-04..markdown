---
layout: post
title: "Python Serverless Microframework, Chalice 사용하기, 04"
date: 2018-05-14 21:58:47 +0900
categories: [python, backend]
encoding: UTF-8
---

<br>
<br>

지금까지 만들어진 서버쪽 

소스 코드는 [https://github.com/lee-seul/recording-server](https://github.com/lee-seul/recording-server) 로 가면 볼 수 있다.

<br>

이전 장에서 설계한 테이블의 구조는 아래와 같다. 


* 회원정보 테이블
    * id (partition key), 유저를 식별하기 위한 값 
    * auth_key (sort key), 앱과의 통신 도중 인증을 위해 필요한 값 

<br>

* 녹음 파일 테이블
    * id(partition key), 파일을 식별하기 위한 값
    * user_id(sort key), 어떤 유저가 업로드한 파일인지를 확인하기 위한 값


<br>

이번 장에서는 DynamoDB를 다루기 위해 사용될 코드를 미리 구현해 보겠다. 

<br>


### 5. DynamoDB 

<br>

DB에 값을 넣고 빼는 등의 코드가 애플리케이션 전반에서 반복적으로 사용될 것이기 때문에 DB 관련 코드부터 먼저 작성한다. 

<br>

현재 프로젝트 디렉토리를 확인 해보면 아래와 같은 형태일 것이다. 


```shell
$ ls -la
drwxr-xr-x   .chalice
-rw-r--r--   app.py
-rw-r--r--   requirements.txt
```

chalice deploy 이후에 AWS Lambda에서 코드가 실제로 어떻게 올라갔는지 확인해보면 app.py 파일만 업로드되어 있는 것을 알 수 있을 것이다. 

app.py에 모든 소스 코드를 넣을 수 없으므로 새로운 파이썬 패키지를 하나 만들어주고 거기에 db_handler.py라는 이름으로 DB를 다루는 코드를 추가한다. 

(chalice deploy를 통해 AWS Lambda에 소스가 배포될 때 app.py와 chalicelib만이 반영된다.)

<br>

```shell
$ mkdir chalicelib
$ cd chalicelib
$ touch __init__.py  # 이러면 파이썬 패키지가 된다. 
$ vim db_handler.py 
```
<br>

```python
# coding: utf-8 

import boto3 


class DynamoDB(object):

    def __init__(self):
        self.conn = boto3.resource('dynamodb', region_name='ap-northeast-2')


```

간단하게 설명하면, DynamoDB라는 클래스를 선언하고 생성자에 conn이라는 이름으로 dynamodb와 연동하였다. 

self.conn을 활용하여 DynamoDB 클래스의 메소드들에서 DynamoDB를 사용할 수 있도록 해줄 예정이다. 

<br>

DynamoDB에서 CRUD를 실행하기 위해서는 테이블이 필요하다. 

그러므로 가장 먼저 테이블을 만들기 위한 메소드를 구현해 보면


```python

""" 윗부분 생략, DynamoDB의 메소드 구현 """

    def create_table(
        self, table_name, hash_dict, attrs, 
        range_dict=None, read_throughput=1, write_throughput=1):  

        dynamodb = self.conn 

        key_schema = [
            {
                'AttributeName': hash_dict['AttributeName'],
                'KeyType': 'HASH'
            }
        ] 
        attrs += [hash_dict]

        if range_dict is not None:
            attrs += [range_dict]
            key_schema += [
                {
                    'AttributeName': range_dict['AttributeName'],
                    'KeyType': 'RANGE'
                }
            ]

        table = dynamodb.create_table(
            TableName=table_name,
            KeySchema=key_schema,
            AttributeDefinitions=attrs,
            ProvisionedThroughput={
                'ReadCapacityUnits': read_throughput,
                'WriteCapacityUnits': write_throughput
            }
        )

        return table 
```

DynamoDB의 테이블은 파티션 키와 정렬 키 2개의 키를 가질 수 있다. 파티션 키의 경우 필수적으로 존재해야 하며 해시 키라고도 불린다. 

정렬 키의 경우는 필수 값은 아니지만 테이블에 정의되면 파티션 키와 함께 복합키로서 역할을 하며, 레인지 키라고도 한다. 

create_table의 매개변수들을 보면 파티션 키의 값인 hash_dict는 필수 값이지만 정렬 키의 값인 range_dict는 필수 값이 아니다. 

attrs는 테이블에 실제로 생성될 필드명과 필드의 데이터 타입에 대해 정의된 리스트를 인자로 받는다. 

attrs에 있는 값들은 모두 키값이거나 키값이 아닌 값은 추가적으로 인덱스 선언을 해주어야하지만 녹음기 앱에서는 추가적인 인덱스가 필요하지 않으므로 

실제 attrs는 빈 리스트만을 넘기고 hash_dict 값과 range_dict 값을 메소드 내부에서 추가해 준다. 

마지막으로 read_throughput과 write_throughput이 있는데 이는 단어 그대로 읽기 성능과 쓰기 성능에 대한 값이다. 

<br>

모든 인자를 받아 boto3 dynamodb의 create_table 메소드를 통해 테이블을 생성한다. 


<br>

다음으로 구현할 메소드는 테이블에 값을 넣기위해 사용될 insert_item이다.


```python

""" 윗부분 생략, DynamoDB의 메소드 구현 """

    def insert_item(self, table_name, item):
        dynamodb = self.conn 

        table = dynamodb.Table(table_name)

        response = table.put_item(Item=item)
        if response['ResponseMetadata']['HTTPStatusCode'] == 200:
            return True

        return False

```

간단한 코드이다. table_name과 아이템이라는 매개변수를 통해 DB에 insert할 값을 받는다. 

dynamodb.Table(table_name)을 통해 table_name에 해당하는 Table에 접근한 후 

put_item이라는 메소드를 통해 딕셔너리 형태의 item을 DB에 넣는 요청을 보낸다. 

요청이 성공했다면 True를 실패했다면 False를 반환하는 코드이다. 

<br>


다음은 특정 아이템을 DB에서 얻어올 때 사용될 get_item을 구현해보겠다. 



```python

""" 윗부분 생략, DynamoDB의 메소드 구현 """

    def get_item(self, table_name, query_item):
        dynamodb = self.conn

        table = dynamodb.Table(table_name)

        response = table.get_itme(Key=query_item)

        if not 'Item' in response:
            return None
        
        item = response['Item']
        return item 
 

```

get_item은 insert_item과 유사한 형태를 가진다. 다른 점은 put_item이 아닌 get_item이라는 메소드를 가지며 

인자로 파티션 키와 정렬 키를 딕셔너리 형태로 저장하고 있는 query_item을 받는다. 

파티션 키만을 식별자로 사용하는 경우에는 query_item에 파티션 키 값만을 전달하고 

파티션 키와 정렬 키가 모두 사용되는 경우에는 두 키 모두를 전달한다. 

<br>

update와 delete는 구조가 get_item이나 insert_item과 유사하기 때문에 한번에 설명하겠다. 


```python

from chalicelib.utils import make_update_expr

""" 윗부분 생략, DynamoDB의 메소드 구현 """

    def delete_item(self, table_name, item_key):
        dynamodb = self.conn 

        table = dynamodb.Table(table_name)

        response = table.delete_item(Key=item_key)
        if response['ResponseMetadata']['HTTPStatusCode'] == 200:
            return True

        return False 

    def update_item(self, table_name, key_dict, update_data):
        dynamodb = self.conn

        table = dynamodb.Table(table_name)
        update_expr, expr_attr_values = make_update_expr(update_data)
        
        response = table.update_item(
            Key=key_dict,
            UpdateExpression=update_expr,
            ExpressionAttributeValues=expr_attr_values
        )
        if response['ResponseMetadata']['HTTPStatusCode'] == 200:
            return True
       
        return False 

```

delete_item은 get_item과 구조가 동일하나 결과가 item을 반환하는 것이 아니라 해당 item을 DB에서 삭제한다. 

update_item의 경우 key_dict에 해당하는 값을 찾아 update_data로 변경하는 메소드이다. 

메소드 내부에 보면 make_update_expr라는 함수가 쓰였는데 이 함수의 코드는 아래와 같다. 


```python
# coding: utf-8
# chalicelib/utils.py


def make_update_expr(update_data:List):
    expr = 'SET'
    last = len(update_data) - 1
    
    expr_attr_values = {}
    for idx, data in enumerate(update_data):
        expr += ' {} = :val{}'.format(data['attribute'], idx+1)
        if not idx == last:
            expr += ','
        key = 'val' + str(idx+1)
        expr_attr_values[key] = data['value']

    return expr, expr_attr_values 


```

boto3의 update_item 메소드를 보면 UpdateExpression과 ExpressionAtrributeValues 라는 키워드 인자들을 받는데 

이 2개의 인자들에 대응하기 위한 함수이다. 업데이트하고 싶은 값을 표현식 형태로 바꿔준다. 


<br>

마지막으로 구현할 메소드는 scan_item이다. 

녹음기 앱에서 DB에 여러값을 한번에 조회하는 경우는 사용자가 녹음한 파일의 리스트를 제공해줄 때 뿐이다. 

특정 사용자가 저장한 녹음 파일만을 검색하기 위해서는 user_id 값으로 쿼리를 수행해야하는데 DyanmoDB에서는 

정렬 키 홀로 쿼리를 수행할 수 없다. 쿼리를 수행하기 위해서는 파티션 키 혹은 파티션 키 + 정렬 키가 필요하기 때문에 

scan_item을 통해 user_id 값, 정렬 키를 사용해 해당 녹음 파일 리스트 API를 구현할 것이기 때문이다. 

scan은 query에 비해 성능에 부하를 줄 수 있으므로 다른 프로젝트에서 사용할 경우 성능에 부하를 얼마나 줄지 고려된 상황에서 사용해야 한다. 


```python

from boto3.dynamodb.conditions import Attr


""" 윗부분 생략, DynamoDB의 메소드 구현 """

    def scan_item(self, table_name, attr_dict):
        dynamodb = self.conn 

        table = dynamodb.Table(table_name)

        response = table.scan(
            FilterExpression=Attr(attr_dict['name']).eq(attr_dict['value'])
        )

        items = []
        if 'Items' in response:
            items = response['Items']
        return items


```

scan은 테이블에 존재하는 어떤 필드로도 실행이 가능한데 여기서 구현된 경우 단 하나의 필드만으로 scan한다. 

Attr()을 통해 필드 값을 정의하고 attr_dict['value']와 일치하는 값들을 반환한다.





<br>
<br>

db_handler.py의 전체 코드이다. 


```python
# coding: utf-8 

import boto3 
from boto3.dynamodb.conditions import Attr

from chalicelib.utils import make_update_expr


class DynamoDB(object):

    def __init__(self):
        self.conn = boto3.resource('dynamodb', region_name='ap-northeast-2')

    def create_table(
        self, table_name, hash_dict, attrs, 
        range_dict=None, read_throughput=1, write_throughput=1):  

        dynamodb = self.conn 

        key_schema = [
            {
                'AttributeName': hash_dict['AttributeName'],
                'KeyType': 'HASH'
            }
        ] 
        attrs += [hash_dict]

        if range_dict is not None:
            attrs += [range_dict]
            key_schema += [
                {
                    'AttributeName': range_dict['AttributeName'],
                    'KeyType': 'RANGE'
                }
            ]

        table = dynamodb.create_table(
            TableName=table_name,
            KeySchema=key_schema,
            AttributeDefinitions=attrs,
            ProvisionedThroughput={
                'ReadCapacityUnits': read_throughput,
                'WriteCapacityUnits': write_throughput
            }
        )

        return table

    def insert_item(self, table_name, item):
        dynamodb = self.conn 

        table = dynamodb.Table(table_name)

        response = table.put_item(Item=item)
        if response['ResponseMetadata']['HTTPStatusCode'] == 200:
            return True

        return False

    def get_item(self, table_name, query_item):
        dynamodb = self.conn

        table = dynamodb.Table(table_name)

        response = table.get_itme(Key=query_item)

        if not 'Item' in response:
            return None
        
        item = response['Item']
        return item 

    def delete_item(self, table_name, item_key):
        dynamodb = self.conn 

        table = dynamodb.Table(table_name)

        response = table.delete_item(Key=item_key)
        if response['ResponseMetadata']['HTTPStatusCode'] == 200:
            return True

        return False 

    def update_item(self, table_name, key_dict, update_data):
        dynamodb = self.conn

        table = dynamodb.Table(table_name)
        update_expr, expr_attr_values = make_update_expr(update_data)
        
        response = table.update_item(
            Key=key_dict,
            UpdateExpression=update_expr,
            ExpressionAttributeValues=expr_attr_values
        )
        if response['ResponseMetadata']['HTTPStatusCode'] == 200:
            return True
       
        return False 

    def scan_item(self, table_name, attr_dict):
        dynamodb = self.conn 

        table = dynamodb.Table(table_name)

        response = table.scan(
            FilterExpression=Attr(attr_dict['name']).eq(attr_dict['value'])
        )

        items = []
        if 'Items' in response:
            items = response['Items']
        return items
```

<br>
<br>


이번 장에서는 DynamoDB를 다루기 위해 boto3를 사용하기 쉽도록 코드로 구현하였다. 

다음 장에서는 실제 DynamoDB 테이블을 코드를 통해 생성해보도록 하겠다. 



<br>
<br>
