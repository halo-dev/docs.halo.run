---
title: 页面变量
description: 每个页面所返回的变量
published: true
date: 2021-01-10T13:36:22.851Z
tags: 
editor: undefined
dateCreated: 2020-10-11T15:14:42.747Z
---

## 首页（index.ftl）

访问路径：`http://yourdomain`

### posts（Object）

#### Tabs {.tabset}
##### 语法

##### 参数

```json
{
  "content": [
    {
      "categories": [
        {
          "createTime": "2020-10-13T13:23:39.143Z",
          "description": "string",
          "fullPath": "string",
          "id": 0,
          "name": "string",
          "parentId": 0,
          "slug": "string",
          "thumbnail": "string"
        }
      ],
      "commentCount": 0,
      "createTime": "2020-10-13T13:23:39.143Z",
      "disallowComment": true,
      "editTime": "2020-10-13T13:23:39.143Z",
      "editorType": "MARKDOWN",
      "fullPath": "string",
      "id": 0,
      "likes": 0,
      "metaDescription": "string",
      "metaKeywords": "string",
      "metas": {},
      "password": "string",
      "slug": "string",
      "status": "PUBLISHED",
      "summary": "string",
      "tags": [
        {
          "createTime": "2020-10-13T13:23:39.143Z",
          "fullPath": "string",
          "id": 0,
          "name": "string",
          "slug": "string",
          "thumbnail": "string"
        }
      ],
      "template": "string",
      "thumbnail": "string",
      "title": "string",
      "topPriority": 0,
      "topped": true,
      "updateTime": "2020-10-13T13:23:39.143Z",
      "visits": 0,
      "wordCount": 0
    }
  ],
  "empty": true,
  "first": true,
  "last": true,
  "number": 0,
  "numberOfElements": 0,
  "pageable": {
    "page": 0,
    "size": 0,
    "sort": [
      "string"
    ]
  },
  "size": 0,
  "sort": {
    "sort": [
      "string"
    ]
  },
  "totalElements": 0,
  "totalPages": 0
}
```

##### 示例

遍历输出首页的文章：

```html

```

## 文章页面（post.ftl）

访问路径不固定，视固定链接配置而定，目前可以配置的有：

- `http://yourdomain/archives/{slug}`（默认）
- `http://yourdomain/1970/01/{slug}`
- `http://yourdomain/1970/01/01/{slug}`
- `http://yourdomain/?p={id}`
- `http://yourdomain/archives/{id}`

### post（Object）

### prevPost（Object）

### nextPost（Object）

### categories（List）

### tags（List）

### tags（Object）

## 自定义页面（sheet.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/s/{slug}`

### sheet（Object）

### metas（Object）

## 文章归档页面（archives.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/archives`

### posts（Object）

### archives（List）

## 分类目录页面（categories.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/categories`

## 单个分类所属文章页面（category.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/categories/{slug}`

### posts（Object）

### category（Object）

## 标签页面（tags.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/tags`

## 单个标签所属文章页面（tag.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/tags/{slug}`

### posts（Object）

### tag（Object）

## 文章搜索结果页面（search.ftl）

访问路径：`http://yourdomain/search?keyword=keyword`

### keyword（String）

### posts（Object）

## 友情链接页面（links.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/links`

## 图库页面（photos.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/photos`

### photos（Object）

## 日志页面（journals.ftl）

访问路径不固定，视固定链接配置而定，默认为：`http://yourdomain/journals`

### journals（Object）