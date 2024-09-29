---
theme: dracula
canvasWidth: 700
layout: cover
text-align: center
title: Optimizing Cache for Saving Cash
---

# Optimizing Cache for Saving Cash 

---

# About me

<v-clicks>

🫶 Passionate about open source projects

🚀 Expert in optimizing web appplications

👴🏻 One of grand-parents of Magento

🧑‍🚒 Experienced in putting out fires on production systems

</v-clicks>

---

---
layout: fact
---

## Cache Types

---
layout: fact
---

<img src="/cache-types.svg">

---
layout: fact
---

<img src="/cache-types-expensive.svg">

---
layout: fact
---

## Ideal Future 

--- 

<img src="/cache-types-better.svg">

--- 

# Low cache hit ratio

<v-clicks>

🗂️ Too much cache variations

🔄 High frequency of updates

🚫 Aggressive cache eviction

</v-clicks>

--- 

# Possible solutions

<v-clicks>

✂️ Reduce cache variations by splitting cache records

🛠️ Remove cache and optimize underlying slow generation

🚫 Avoid flushing cache too often

⚙️ Atomic cache writes implementation

</v-clicks>

---

# Example: Layout Cache


<v-clicks>

🔑 Each unique entity has its own cache key for layout

🧩 Most of the layout is the same across entity type

📈 On large catalogs, cache can grow into 20Gbytes+ overtime

</v-clicks>

---

# Source: Entity Specific Handles

```php {all|4,9}
public function addPageLayoutHandles(
  array $parameters = [], 
  $defaultHandle = null, 
  $entitySpecific = true
 ) {
    // ...
    foreach ($parameters as $key => $value) {
        // ...
        $pageHandle = $handle . '_' . $key . '_' . $value;
        $pageHandles[] = $pageHandle;
        if ($entitySpecific) {
            $this->entitySpecificHandlesList->addHandle($pageHandle);
        }
    }
}
```

---

# Quickfix

<v-clicks>

🔌 Create plugin for `Magento\Framework\View\Result\Page`

✅ If `$entitySpecific` parameter equals true, return without executing the original method

</v-clicks>


---

# DI Config

```xml
<type name="Magento\Framework\View\Result\Page">
    <plugin name="performance_entity_cache_removal"
            type="PerformanceTraining\LayoutCache\Plugin\RemoveEntityCache"
            sortOrder="10"/>
</type>
```

---

# Plugin Code

```php
<?php

class RemoveEntityCache
{

    public function aroundAddPageLayoutHandles(
        Page $subject, callable $proceed, array $parameters = [], 
        $defaultHandle = null, $entitySpecific = true
    ) {
        if ($entitySpecific) {
            return true;
        }
        return $proceed($parameters, $defaultHandle, $entitySpecific);
    }
}
```

---

# Example: Configurable Products

<v-clicks>

🔄 Each time you use `getUsedProducts()` data gets cached in Redis

📈 Cache size grows with your catalog

🚫 Useless as it gets flushed on almost every product sale

</v-clicks>

---

# Quickfix

<v-click>

🚫 Disable `used_products_cache` plugin on `Magento\ConfigurableProduct\Model\Product\Type\Configurable`  

</v-click>


---

# DI Config

```xml
<type name="Magento\Framework\View\Result\Page">
    <plugin name="performance_entity_cache_removal"
            type="PerformanceTraining\LayoutCache\Plugin\RemoveEntityCache"
            sortOrder="10"/>
</type>
```

---

# Result

😱 With both fixes applied it reduces max Redis cache usage to under 10Mbytes on 1 million SKU data set

---
layout: two-cols
---

# Thank You!

## Support Charities

- Ukrainian Foundation "Come Back Alive"

  https://savelife.in.ua/en/


- Dutch Foundation "Sails of Freedom"

  https://zeilenvanvrijheid.nl/

::right::

# &nbsp;

## Help Ukraine 🇺🇦

<img src="/qrcode-come-back-alive.png" class="w-20 ml-10 mt-8  shadow"  /> 

<img src="/qrcode-zeilen.png"  class="w-20 ml-10 mt-8 shadow"  />
