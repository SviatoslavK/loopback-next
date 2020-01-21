---
lang: en
title: 'Migrating custom model methods'
keywords: LoopBack 4.0, LoopBack 4, LoopBack 3, Migration
sidebar: lb4_sidebar
permalink: /doc/en/lb4/migration-models-methods.html
---

## Introduction

In LoopBack 3, a model class has three responsibilities: a model describing shape of data, a repository providing data-access APIs, and a controller implementing REST API.


In LoopBack 4:

- data-access APIs are implemented by repositories that are decoupled from models
- the REST APIs are implemented by controllers that are decoupled
from models.


