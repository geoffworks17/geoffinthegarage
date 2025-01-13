---
title: "{{ replace .Name "-" " " | title }}"
date: {{ .Date }}
thumbnail:
    src: "/blog/{{ .Name }}/media/_imagethumb.jpg"
    visibility:
        - list
categories:
    - "_Category"
tags:
    - "_tag1"
    - "_tag2"
draft: true
---
Preview Text

<!--more-->

Main Text