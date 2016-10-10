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
* ```$alias```: ```string``` the alias for the expression
* ```$expression```: ```string``` the expression
* ```$attribute```: ```string|array``` the attributes list for replacement in ```$expression```

### Examples
get customer name by concatenating the firstname and lastname:

```php
<?php
$collection = Mage::getResourceModel('customer/customer_collection');
$collection->addExpressionAttributeToSelect('name', {% raw %}'CONCAT({{firstname}}, \' \', {{lastname}})'{% endraw %}, array('firstname', 'lastname'));
```

the result select:

```
SELECT
  `e`.*,
  `at_firstname`.`value`                             AS `firstname`,
  `at_lastname`.`value`                              AS `lastname`,
  CONCAT(at_firstname.value, ' ', at_lastname.value) AS `name`
FROM `customer_entity` AS `e`
  LEFT JOIN `customer_entity_varchar` AS `at_firstname`
    ON (`at_firstname`.`entity_id` = `e`.`entity_id`) AND (`at_firstname`.`attribute_id` = '5')
  LEFT JOIN `customer_entity_varchar` AS `at_lastname`
    ON (`at_lastname`.`entity_id` = `e`.`entity_id`) AND (`at_lastname`.`attribute_id` = '7')
WHERE (`e`.`entity_type_id` = '1')
```

# Other useful functions

* ```getTable($table)```: return the fullname of ```$table```
* ```getAllIds()```: Retrieve all ids for collection
* ```save()```: save all items in collection
* ```delete()```: delete all items in collection
* ```toArray($arrAttributes = array())```: export items data to array, with all keys in ```$arrAttributes```