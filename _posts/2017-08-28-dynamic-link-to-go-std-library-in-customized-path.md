---
layout: post
title:  "Dynamically link to Go std library using customized path"
date:   2017-08-28
desc: "Dynamically link to Go std library using customized path"
keywords: "Golang,link,gh-pages,website,blog,easy"
categories: [golang]
tags: [golang,linker]
icon: icon-html
---
While I was reading golang's new feature of sharing golang packages in the form of `.so` file, I found this nice [blog](http://blog.ralch.com/tutorial/golang-sharing-libraries/). I describes how to create a shared library object (DSO) in go and link to it from go as well. However, the created go std library's link path turned out to be golang's default installation path. In my case it is `/usr/local/go/pkg/linux_amd64_dynlink/libstd.so`.   

