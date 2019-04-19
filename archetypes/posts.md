---
author: "{{ .Site.Author.name }}"
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
draft: true
categories:
- category1
- category2
tags:
- tag1
- tag2
keywords:
- keyword1
- keyword2
comments: true
---

<!--more-->
