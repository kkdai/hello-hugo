---
author: kkdai
categories:
- VC++程式設計
- XML程式設計
comments: true
date: "2006-06-29T09:13:31Z"
slug: search-from-attributes-use-xpath
title: Search from attributes use XPATH
wordpress_id: 425
---

To search a attribute via XPATH. you can use Element[@Attr] to address it in XPATH.

So~~ to search some specific search as follow:

1. product@cost which is > 5000, you can write as follow.

_product[@cost > '5000']_

2. product@name contains "yellow" (case-insensitivity).

_contains(translate(product[@name], 'ABC...Z', 'abc....z'), translate('yellow', 'ABC...Z', 'abc....z'))_
