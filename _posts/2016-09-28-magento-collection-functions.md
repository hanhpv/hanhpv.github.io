---
layout: post
title: "Magento 1 Collection functions"
author: "Hans Phung"
categories: magento1
permalink: /category/magento1/magento-collection-functions
---
In this article, we cover some useful functions of Magento collection class.

# addAttributeToSelect


### Usage
Add attribute to entities in collection

This function is only available in EAV collection class, which extends from  ```Mage_Eav_Model_Entity_Collection_Abstract```

### Definition
```php
<?php
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
* ```$joinType``` can have one of three values: ```boolean```, ```inner```, ```left```. If ```$joinType``` is specified, the attribute value table will be joined in the sql select, then there is no need to use another query to get the attribute value.

# joinAttribute


### Usage
Join attribute value table to select

This function is only available in EAV collection class, which extends from  ```Mage_Eav_Model_Entity_Collection_Abstract```

### Definition
```php
<?php
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
<?php
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

We can join the attribute of other entity type like below. This will return the ```postcode``` value of default customer billing address.

```php
<?php
$collection = Mage::getResourceModel('customer/customer_collection')
                          ->addAttributeToSelect('email')
                          ->addAttributeToSelect('created_at')
                          ->joinAttribute('billing_postcode', 'customer_address/postcode', 'default_billing', null, 'left');
```
The resulted SELECT

```
SELECT
  `e`.*,
  `at_default_billing`.`value`  AS `default_billing`,
  `at_billing_postcode`.`value` AS `billing_postcode`
FROM `customer_entity` AS `e`
  LEFT JOIN `customer_entity_int` AS `at_default_billing`
    ON (`at_default_billing`.`entity_id` = `e`.`entity_id`) AND (`at_default_billing`.`attribute_id` = '13')
  LEFT JOIN `customer_address_entity_varchar` AS `at_billing_postcode`
    ON (`at_billing_postcode`.`entity_id` = `at_default_billing`.`value`) AND
       (`at_billing_postcode`.`attribute_id` = '30')
WHERE (`e`.`entity_type_id` = '1')
```

# joinField

### Usage
Join regular table field and use an attribute as foreign key

This function is only available in EAV collection class, which extends from  ```Mage_Eav_Model_Entity_Collection_Abstract```

### Definition
```php
<?php
public function joinField($alias, $table, $field, $bind, $cond=null, $joinType='inner')
```

### Parameters
* ```$alias```: the alias of joined filed in select (ex: ```group_name_name```)
* ```$table```: the table to join (ex: ```customer/customer_group```)
* ```$field```: the exactly field name of ```$alias``` (ex: ```customer_group_code```)
* ```$bind```: the join condition, in form of ```joined_field = attribute_code_of_main_entity```
* ```$cond```: additional condition
* ```$joinType```: ```left``` or ```inner```

### Examples
Add customer group name to customer collection entities:

```php
<?php
$collection = Mage::getModel('customer/customer')->getCollection();
$collection->joinField('group_name', 'customer/customer_group', 'customer_group_code', 'customer_group_id=group_id');
```
And the result select:

```
SELECT
  `e`.*,
  `at_group_name`.`customer_group_code` AS `group_name`
FROM `customer_entity` AS `e`
  INNER JOIN `customer_group` AS `at_group_name`
    ON (at_group_name.`customer_group_id` = e.group_id)
WHERE (`e`.`entity_type_id` = '1')
```

# joinTable

### Usage
Join a table

### Parameters
```php
<?php
public function joinTable($table, $bind, $fields = null, $cond = null, $joinType = 'inner')
```
* ```$table```: ```string|array``` name of the table to join, use ```array``` to specify table alias
* ```$bind```: join condition
* ```$fields```: ```string|array``` fields to select
* ```$cond```: ```null|array``` where condition
* ```$joinType```: ```inner``` or ```left```

### Examples
```php
<?php
$collection =  Mage::getResourceModel('wishlist/product_collection');
$collection->joinTable(
    array('t_wi' => 'wishlist/item'),
    'product_id = entity_id',
    array(
        'product_id'                => 'product_id',
        'wishlist_item_description' => 'description'
    ),
    array(
        'wishlist_id'               => '1',
        'store_id'                  => array('in' => [3])
    )
);
```
result select:

```
SELECT
  `e`.*,
  `t_wi`.`product_id`,
  `t_wi`.`description` AS `wishlist_item_description`
FROM `catalog_product_entity` AS `e`
  INNER JOIN `wishlist_item` AS `t_wi`
    ON (t_wi.product_id = e.entity_id) AND (t_wi.wishlist_id = '1') AND (t_wi.store_id IN (3))
```