## Best Price XML Feed

The structure of the XML file containing the products of a merchant coming from Shopify must pass under certain requirements, very similar to Skroutz:

**Best Price Requirements:**

- The product list must be sent in an XML file
- The XML file must have proper encoding, and that must be included in the XML head of the file
- A date tag must include the file creation date at the beginning of the XML file (date format must be YYYY-MM-DD HH:MM)
- Every product must be listed only once (unique identifier)

Let's go through the rest of the **required attributes** that must be included in the XML file:

- productId (str): product's unique identifier
- title (str): title of the product, must include manufacturer, model, color or other options that make it unique from similar products (variants)
- productURL (str): the URL that links to the product in the shop's page, must be valid, not containing the connection's session id, not leading to a category/collection and not requiring register/login from the user to view the product
- imageURL (str): the URL that links to the image of the product, in case of additional images we can either encase each image as an imgX tag within a imagesURL tag, or we can pass in the primary image and then all the others as additional images.
- price (decimal): the product's retail price containing tax, can have any format with dots and commas
- categoryPath (str): the full path that leads to the final category of the product
- availability (str): the estimated time of product shipping, Best Price has a list of available options for this field:
    - Σε απόθεμα
    - Παράδοση σε 1-3 ημέρες
    - Παράδοση σε 4-7 ημέρες
    - Παράδοση σε 4-10 ημέρες
    - Παράδοση σε 8-14 ημέρες
    - Κατόπιν παραγγελίας
    - Προπαραγγελία
    - Εξαντλήθηκε
    Since it is a string field, we can insert whatever value we want, however Best Price will match our input to one of these options above.
- brand (str): the manufacturer of the product
- MPN / barcode / EAN / SKU (str): the unique manufacturer identifier of the product

There are some fields that required for certain items:

- ISBN (str): the ISBN string with or without dashes, required for books
- size (str): in case the product is available in multiple sizes, there must be a field that lists out all of them, a string of all sizes in commas

Other fields not necessarily required:

- categoryID (str): the id of the category that the product belongs to in the store 
- stock (List - Y/N): the stock status of the product, can be either Y or N, as in yes or no. If the field is empty however, the product availability is set to "Κατόπιν παραγγελίας".
- color (str): the color of the product, products that come in different colors must be listed as different products
- warranty_provider (List - GR/EU/STORE): the provider of the product warranty
- warranty_duration (str): the product's warranty duration
- isBundle (List - Y/N): if the product belongs to a bundle, this field must be set to 'Y'
- features (HTML table): the description of the product (technical characteristics etc) must be contained within an HTML or XML table
- weight (decimal/str): the weight of the product in grams, or in another unit, if not in grams then it must be followed by that specific unit (for example 5250 / '5.25Kg') 
- shipping (decimal): the extra price of shipping, included only if this shipping cost is always the same for any order (if free, then set to 0)

**Important notes:**
- Shopify provides us with variants and we list them as separate products
- Need to check out whether a product that is not in stock is listed or not in the Best Price website
- How are category paths created for our products (product tags?)
- Availability
- In stock