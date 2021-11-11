## Available Options

Every shop that installs our application, can choose some options that will define and differentiate the way the shop's products will be mapped in the XML form. One shop can have many options and one option can belong to many shops (many-to-many relationship). Also, since we have multiple xml variations, the same option for the same shop can have a different value in different Shopify apps.

1. **Delivery time** (select): Default delivery time that fills the availability field for all products (multiple choice based on the list of provided options, or custom through user input) (DB)
2. **Out of stock delivery time** (select): If product is out of stock, merchant can select another availability time from the same Skroutz list of availabilities (multiple choice based on the list of provided options, or custom through user input) (DB)
3. **Custom domain** (text): Custom domain option that replaces product url domain for all products (DB)
4. **Separate variants** (checkbox): By default, variants are grouped in one product, choices are Separate none, Separate all except size, Separate all (DB)
5. **Specific collection** (select): Get products only from specific collection (DB)
6. **Compare at price field** (checkbox): Include the compare_at price in each XML product (DB)
7. **Delivery time based on product tags** (boolean): Availability through product tag "availability:_%s_" (or "diathesimotita=_%s_" for old customers who still have it saved this way) (tag+DB)
8. **Product title based on product tags** (boolean): Change product title with tag "title:_%s_" (tag+DB)
9. **Image URL based on product tags** (boolean): Change image of first variant with tag "image:_%s_" (tag+DB)
10. **Product URL based on product tags** (boolean): Change product url with tag "url:_%s_" (tag+DB)
11. **Product Glami CPC based on product tags** (boolean): Change product Glami CPC with tag "glamicpc:_%0.2f_" (tag+DB)
12. **Product material based on product tags** (boolean): Change product material with tag "material:_%s_|percentage=_%0.2f_" (tag+DB)
13. **Exclude from xmlVariation** (boolean): Exclude product from XML with tag "exclude-_xmlVariation_" (tag+DB)
14. **Category tree** (json): Product types mapped to proper xml variation categories, value saved as a json encoded string (DB)
15. **Skip Required** (checkbox): Just in case some clients don't want to deal with the required fields validation, this option overrides this step and passes all products in the XML, no matter what properties are wrong or lacking (DB)
16. <strong style="color: red">Generic Feed (text)</strong>: This option is added manually from the developers after communicating with the merchant, to choose the variation of the XML that will be created **(ONLY FOR THE GENERIC SYN APP)**.

Possible options for new variations:

Energy efficiency class
Tire type/model
Shipping cost