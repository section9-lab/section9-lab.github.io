---
title: FastAPI
date: 2023-11-22
category:
  - Tools
tag:
  - python
star: false
sticky: false
---

# FastAPI


## start
> uvicorn main:app --reload

## hello world
```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()

# http://127.0.0.1:8000/
@app.get("/")
async def root():
    return {"message": "Hello World"}

if __name__ == '__main__':
    uvicorn.run(app='main:app', reload=True)
```
## rest
```python
最基本的路径参数
# http://127.0.0.1:8000/items/1
@app.get("/items/{item_id}")
async def read_item(item_id):
    return {"item_id": item_id} # item_id自定义
    
多个路径参数
# http://127.0.0.1:8000/items/1/2
@app.get("/items/{item_id}/{user_id}")
async def read_item(item_id, user_id):
    return {"item_id": item_id, "user_id": user_id}
    
有类型的路径参数
# http://127.0.0.1:8000/items/1
@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
    
文件路径参数
# http://127.0.0.1:8000/file//home/my/my.txt
@app.get("/file/{file_path:path}")
async def read_item(file_path):
    return {"file_path": file_path}
```

## rest参数
```python
带默认值的查询参数
# http://127.0.0.1:8000/items/?skip=0&limit=2
fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]
@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip: skip + limit]
    
可选查询参数
# http://127.0.0.1:8000/items/1?q=admin
from typing import Union
@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Union[str, None] = None):
    if q:
        return {"item_id": item_id, "q": q}
    return {"item_id": item_id}
    
多路径多查询参数
# http://127.0.0.1:8000/users/1/items/2
# or 
# http://127.0.0.1:8000/users/1/items/2?q=query&short=true
@app.get("/users/{user_id}/items/{item_id}")
async def read_user_item(
        user_id: int, item_id: str, q: Union[str, None] = None, short: bool = False
):
    item = {"item_id": item_id, "owner_id": user_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
    
必需查询参数
# http://127.0.0.1:8000/items/123?needy=yes
@app.get("/items/{item_id}")
async def read_user_item(item_id: str, needy: str):
    item = {"item_id": item_id, "needy": needy}
    return item
```

## 请求体
```python
from pydantic import BaseModel
from typing import Union

class Item(BaseModel):
    name: str = '小明'
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None
@app.post("/items/")
async def create_item(item: Item):
    print(item.name)
    return item
    
调用
curl -X 'POST' \
  'http://127.0.0.1:8000/items/' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "小明",
  "description": "string",
  "price": 0,
  "tax": 0
}'
```

## 参数查询&字符串校验
```python
from fastapi import Query

@app.get("/items/")
async def read_items(q: Union[str, None] = Query(default=None, max_length=50)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

## 参数列表
```text
default	默认值	任意类型或...
max_length	最大长度	int
min_length	最小长度	int
pattern	正则匹配	string
alias	别名参数	string
deprecated	准备弃用参数	bool
```

## 多参数同时查询
```python
# http://127.0.0.1:8000/items/?q=foo&q=bar
@app.get("/items/")
async def read_items(q: Union[List[str], None] = Query(default=None)):
    query_items = {"q": q}
    return query_items
```

## 路径参数&数值校验
```python
from fastapi import FastAPI, Path, Query
from typing_extensions import Annotated

@app.get("/items/{item_id}")
async def read_items(
    item_id: Annotated[int, Path(title="The ID of the item to get")],
    q: Annotated[Union[str, None], Query(alias="item-query")] = None,
):
    results = {"item_id": item_id}
    if q:
        results.update({"q": q})
    return results
```
```text
参数列表
...	和Query具有相同参数	...
ge	大于等于	int float
gt	大于	int float
le	小于等于	int float
le	小于等于	int float
title	api文档的标题	string
```

## 表单
```python
from fastapi import FastAPI, Form
import uvicorn

app = FastAPI()
@app.post("/login/")
async def login(username: str = Form(), password: str = Form()):
    return {"username": username}
if __name__ == '__main__':
    uvicorn.run(app='main:app', reload=True)
```