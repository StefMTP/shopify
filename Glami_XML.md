## Glami XML Feed

**Glami Requirements:**

- The product list must be sent in XML format
- The XML file must have proper encoding, and that must be included in the XML head of the file
- Every color and size of the same product must be given as a different shop item
- Product names must not include size
- Out of stock products must not be included
- XML tags must be in capital letters

Let's go through the rest of the **required attributes** that must be included in the XML file:

- ITEM_ID (str): unique identifier for every product
- PRODUCTNAME (str): the title of the product, must include necessary info like gender, material, color (not the size or other commercial info)
- DESCRIPTION: the description of the product, specification and features, must be in Greek (it isn't assigned as required in the Glami Feed specification, but in the parameter's description it says it is required for every product)
- URL (str): the URL that links to the shop's page for the product
- IMGURL (str): the URL that links to the product's image
- PRICE_VAT (decimal): the product's retail price including VAT, using comma or dot for decimal values
- MANUFACTURER (str): the product brand (if there is no brand, just type "OEM")
- CATEGORYTEXT (str): detailed category path, aligned with Glami's categories (https://www.glami.gr/category-xml/). Every product must have only one category, must always include category concerning gender and age (γυναίκες, άνδρες, κορίτσια, αγόρια)
- SIZE (str): the size of the product, every item must have its own indpendent size (list of Glami sizes: https://drive.google.com/file/d/1Nk__dz7nJ13bM2ve5rkuVJnrFP6bPITq/view), this parameter comes as a param tag, with a param_name tag (inner text like "μέγεθος") and a val tag (inner text like "41") inside
- DELIVERY_DATE (integer): how many days needed to deliver a product (άμεσα διαθέσιμο = 0, παράδοση παραγγελίας εντός 5 ημερών = 5)

Other attributes that are **not necessarily required**:

- PARAM (str / integer): tags like μέγεθος, εφαρμογή, υλικό, χρώμα, χώρα προέλευσης, βιωσιμότητα are inserted into param tags (with param_name and val inside)
- ITEMGROUP_ID (str): this attribute can be used to group the variations of the same product with different colors/sizes. Essentially, since in Shopify every product comes with its own id and every product has variants (all the possible combinations of variant options), the product id can be used here.
- URL_SIZE (str): a specific url for the product at a certain size
- IMGURL_ALTERNATIVE (str): links for additional images of the product (at the same size and color)
- GLAMI_CPC (decimal): allows the user to set a custom CPC (no idea what this is)
- PROMOTION_ID (integer): the id of the discount coupon set from the merchant, allows the user to apply discount coupons on the product
- MATERIAL (str): the material of the product, must be in a PARAM tag
- SIZE_SYSTEM (str): based on what system the size was decided
- DELIVERY (str and integer): not used in Greece yet

**Important notes:**

- The required attributes come in their own tags, the rest come into param tags with name and value