## Skroutz XML Feed

Skroutz allows merchants to send data about their products, taken from their Shopify stores, to Skroutz's body, so that they can be included and listed on skroutz.gr. However, Skroutz has its own way of accepting product information.

**Skroutz requirements:**

- The product list must be sent in an XML file
- The XML file must have proper encoding, and that must be included in the XML head of the file
- A created_at tag must include the file creation date at the beginning of the XML file
- Price must include tax (price with vat)
- Every product must be listed only once


Apart from these specifications, there is a list of **required attributes** that must be included for every product, if we want the XML file to be valid. These are:

- Unique ID (str): the unique identifier of the product
- Name (str): the title of the product, must include necessary details like manufacturer, product options etc.
- Product Link (str): the URL that links to the shop's page for the specified product (must be valid with https certificate)
- Image Link (str): the URL that links to the product's image
- Category Name (str): the product's full category path (Must look like 'Computers > External > Hard Drives')
- Price (decimal - 0.2f): the product's retail price including VAT, up to two decimal places
- Availability (str): the product's shipping availability, Skroutz uses a fixed set of availability descriptions provided below
    - Available in store / Delivery 1 to 3 days
    - Delivery 1 to 3 days
    - Delivery 4 to 10 days
    - Delivery up to 30 days
    They are crosslinked to the ones provided in our feed, but we can practically write anything since it is a string field
- Manufacturer (str): the product's manufacturer (or vendor for example Apple, Samsung, Microsoft etc.)
- MPN / ISBN (str): the unique manufacturer identifier of the product

There are also some fields that are **required on certain items**, these are: 

- Description (str): the description/explanation of the product (strongly recommended for all products)
- Size (str): all available sizes for the product can be included separated by commas (required for fashion items)
- Additional Image Link (str): URL that points to additional photos of the product (required for fashion items)
- Weight (decimal): the weight of the product in grams or kgs (required when shops use weight for their shipping cost rules)
- Color (str): the color of the product (products with different color options must be sumbitted as different products, color is required for fashion items)

Other attributes that are **not necessarily required**:

- EAN/barcode (int): the barcode of the product, international article number for identification in retail products
- InStock (List - Y/N): the stock status of the product, can be either Y or N, as in yes or no (when it is not provided, the default attribute value of the product will be N)
- Shipping Costs (decimal): the shipping cost of the product regardless of customer location, if it is known beforehand it should be included (free shipping can be indicated by providing 0 as a value)

**Important notes:**
- Shopify provides us with variants and we list them as separate products
- Need to check out whether a product that is not in stock is listed or not in the Skroutz website
- How are category paths created for our products - **Merchant links each product type to a category path**
- Availability
- In stock

#### Data feed validator
https://validator.skroutz.gr/
https://engineering.skroutz.gr/blog/the-skroutz-feed-validator/