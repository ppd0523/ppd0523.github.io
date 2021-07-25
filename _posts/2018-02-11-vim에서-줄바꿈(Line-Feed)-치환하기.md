---
layout: post
title: vim에서 줄바꿈(Line Feed) 치환하기
date: 2018-02-11 18:00:00 +0900
categories: [linux, util]
tag: [vi, vim, editor, replace]
toc: false
comments: true
---



vim 문자열 치환. before를 after로 치환한다.

```
:%s/before/after/g
```

vim에서 문자열을 줄바꿈 문자로 치환할 때 `\n`로 대체하면 `^@` 따위로 바뀌고 실제로 줄바꿈이 되지 않는다.

`\n` 대신 `\r`로 하면 줄바꿈 된다.

```
:%s/\n/\r/g
```

%s는 문서 처음, g는 문서 끝을 의미한다. 문서 처음부터 끝까지 문자열을 치환하라는 의미
