---
layout: post
title: "Magento 1 Collection functions"
author: "Hans Phung"
categories: magento1
---
In this article, we cover some functions of Magento collection class.

# addAttributeToSelect
---


### Usage
Add attribute to entities in collection

This function is only available in EAV collection class, which extends from  ```Mage_Eav_Model_Entity_Collection_Abstract```

### Definition
```php
/**
 * Add attribute to entities in collection
 *
 * If $attribute=='*' select all attributes
 *
 * @param   array|string|integer|Mage_Core_Model_Config_Element $attribute
 * @param   false|string $joinType flag for joining attribute
 * @return  Mage_Eav_Model_Entity_Collection_Abstract
 */
public function addAttributeToSelect($attribute, $joinType = false)
```
* ```$attribute``` is the attribute code we want to select, use '*' to select all attributes
* ```$joinType``` can have one of three values: ```true```, ```inner```, ```left```. If ```$joinType``` is specified, the attribute value table will be joined in the sql select, then there is no need to use another query to get the attribute value.

# joinAttribute
---


### Usage
Join attribute value table to select

### Definition
```php
public function joinAttribute($alias, $attribute, $bind, $filter=null, $joinType='inner', $storeId=null)
```

### Parameters
* ```$alias```: the alias of the joined attribute in SELECT
* ```$attribute```: in ```entity_type/attribute``` form (ex: ```catalog_product/name```)
* ```$bind```: attribute of the main entity to link with joined ```$filter```
* ```$filter```: primary key for the joined entity, default is ```entity_id```
* ```$joinType```: ```inner``` or ```left```
* ```$storeId```: the store Id to get the attribute value

### Example
```php
$collection = Mage::getModel('catalog/product')->getCollection();
$collection->joinAttribute('name', 'catalog_product/name', 'entity_id', null, 'inner',Mage_Core_Model_App::ADMIN_STORE_ID)
```
the result SELECT

```
SELECT
  `e`.*,
  `at_default_name`.`value` AS `default_name`
FROM `catalog_product_entity` AS `e`
  INNER JOIN `catalog_product_website` AS `product_website`
    ON product_website.product_id = e.entity_id AND product_website.website_id = '3'
  INNER JOIN `catalog_product_entity_varchar` AS `at_default_name`
    ON (`at_default_name`.`entity_id` = `e`.`entity_id`) AND (`at_default_name`.`attribute_id` = '63') AND
       (`at_default_name`.`store_id` = 0)
```