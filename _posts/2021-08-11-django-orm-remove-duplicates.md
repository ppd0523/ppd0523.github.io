---
layout: post
title: DJANGO ORM 중복 제거하기
date: 2021-08-11 06:00:00 +0900
categories: [web, backend]
tag: [django, ORM, query, data, duplicate, remove, delete]
toc: false
comments: true
---

장고에서 중복 데이터를 삭제하는 방법

* `distinct()` : 고유한 쿼리셋 반환
* `values_list(field1, field2, ...)` : 필드만 포함하는 쿼리셋 반환
* `filter(field1=value1, field2=value2, ...)`

```python
# create_date가 고유한 queryset 얻기
dates = Price.objects.values_list('create_date', flat=True).distinct()

for date in dates:
    # 중복을 제거할 정보 획득
    codes = Price.objects.values_list('stock_code', flat=True).distinct()

    for code in codes:
        # 중복하는 queryset의 id만 획득
        ids = Price.objects.filter(stock_code=code, create_date=date).values_list('id', flat=True)

        # 중복 id 리스트를 슬라이스 하여 전달
        Price.objects.filter(pk__in=ids[1:]).delete()
```

```python
# 고유 조건이 여러개인 queryset 의 경우 아래와 같이

qs = Price.objects.values_list('stock_code', 'stock_name').distinct()
for q in qs:
    code, name = q['stock_code'], q['stock_name']
    ...
```
