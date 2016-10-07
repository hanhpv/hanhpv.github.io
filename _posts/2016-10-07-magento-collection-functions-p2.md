---
layout: post
title: "Magento 1 Collection functions (Part 2)"
author: "Hans Phung"
categories: magento1
permalink: /category/magento1/magento-collection-functions-p2
---
In the [previous article](/category/magento1/magento-collection-functions), we covered some useful functions in Magento collection object. I don't want to write a long post, so I break it into this part.

# addExpressionAttributeToSelect

### Usage
Add attribute expression (SUM, COUNT, etc)

### Parameters
```php
<?php
public function addExpressionAttributeToSelect($alias, $expression, $attribute)
?>
```
* ```$alias```: 