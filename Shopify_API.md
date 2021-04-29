## Working with Shopify: Developing Apps and Interacting with its APIs

### Concepts and terms to know about Shopify before starting:

Ecommerce = buying and selling online
Merchant = someone who sells items/services
Online store = the ecommerce website of a merchant
Store = a retail establishment for selling to customers
Collections = products grouped together into categories
Variant = products come in different options in a lot of stores, for example COLOR, or SIZE. That is why, merchants can add variants to a product to distinguish it. Each product may come with different combinations of various options. Each combination of options is a variant of each respective product.
Inventory = a store's complete list of VARIANTS
Item = one unit of a product (if there are variants, an item is one unit of a variant)

#### Search Syntax of the Shopify API

Reference to how queries can be structured and what keywords can be used.
https://shopify.dev/concepts/about-apis/search-syntax

#### API response codes

- **2XX**: 200 - OK, 201 - resource created, 202 - accepted and processing
- **3XX**: 303 - follow the response on the Location header for a different URL
- **4XX**: 400 - bad request (content type, bad syntax), 401 - unauthorized, 402 - payment required to unfreeze, 403 - forbidden (can't access resource based on the scopes), 404 - not found, 406 - not acceptable headers in the request, 422 - unprocessable entity, 423 - shop is currently locked, 429 - too many requests based on rate limit
- **5XX**: 500 - internal server error, 501 - endpoint not implemented on particular shop, 503 - service unavailable, 504 - gateway timeout (Shopify waits up to 10 seconds for a response)

#### Using the Shopify CLI

The CLI offers an easy way to automate Shopify app creation and setting up.
Xcode - Homebrew - Shopify and ngrok will be useful, even though most things will be settled through the Shopify CLI.
Shopify CLI for Node comes with some commands that can help us test our app, such as populating our store with example products, customers, orders.

### Inside the App

After we've managed to create the app and embed it to our development store, we can start looking at various different things. First of all, we have Next JS which handles the server side rendering and builds our pages using React for the app's frontend. Polaris is a CSS framework that we can use to create a Shopify app that is appropriately themed to fit Shopify's online environment. It comes with its own design system and provides guidelines for the content and how it should be shaped to have a place in the Shopify ecosystem.

Useful points extracted from developing Shopify store apps: embedded apps and the api bridge, ngrok for tunnelling, shopify CLI making some very complicated stuff a little bit more bearable.

### Making API calls to an App

Making sure we have enabled the creation and testing through PRIVATE APPS, we create a new one and copy the api key and password that is given to us. Through Postman, we make requests to our shop on behalf of its private apps like this: {api-key}:{api-token}@{store-url}.admin/api/{api-version}/{api-resource}. Shopify requires this HTTP authentication.

#### Authenticate on Custom and Public Apps

Public and custom apps authenticate differently. While private apps allow direct access with the api key and password, in public and custom Shopify apps, authentication is achieved through **OAuth**.
When a merchant (user) makes a request to install a public/custom app for the first time, the application (client) redirects them to Shopify, that loads the OAuth grant screen and requests the required scopes. Shopify displays the required scopes and prompts the merchant to log in. Once the merchants agrees on the scopes, they are redirected to the redirect_uri (set by the app, a ngrok tunnel in our case). The application requests an access token from Shopify including the client_id, client_secret and code. Shopify responds with the access token and the requested scopes. Now, the app has the access token and can make requests to the Shopify REST API for data.

From our point of view, as creators of such an app, in order to send the merchant the prompt to install it, we are practically accepting a GET request on the app url we have set. The request parameters will include the shop, timestamp and hmac. From our side, we must remove the hmac from the query and process it through sha256 decryption using the app secret. If the authenticity of the request is verified, then we show the prompt to the user, by redirecting them to this URL:

https://{shop}.myshopify.com/admin/oauth/authorize?client_id={api_key}&scope={scopes}&redirect_uri={redirect_uri}&state={nonce}&grant_options[]={access_mode}

Where **shop** is the name of the user's shop, **api_key** is the api key of the app, **scopes** is a list of scopes we require from the user to grant us in order for the app to work, **redirect_uri** is the URL the user is redirected after authorizing the app, **nonce** is a unique random value for every authorization request that must be the same in the next step, and **access_mode** is either online (meant for user interactions that work with web sessions) or offline (meant for long term access to a store with no user interaction, for example something that requires automated background work such as webhooks).

When the user clicks to install the app, they are redirected as specified above and the access code is passed in the confirmation redirect. Again, security checks must be made, by checking the nonce, hmac and shop parameters. If everything is ok, a POST request is made from our side to the shop's access_token endpoint (https://{shop}.myshopify.com/admin/oauth/access_token), making sure that in the body of the request, the client_id, the client_secret and code are provided. The response comes back with the access_token value (string) and scopes (a string separated by commas). If we had requested for online access mode, in the redirection step, then we also get some more data (when the token expires, information about the user etc.).

Now that the application has the API access token, it can use it as a 'X-Shopify-Access-Token' header to make authenticated requests to shop REST APIs.

#### Things to watch out for on the API and its docs

In order to debug properly with Postman, the app needs to be under the private scope. Making requests by looking up the proper urls on the browser works for any app, but through Postman, public or custom apps don't behave in the same way (there's oAuth in the way). Make a private app and set the scopes for any endpoints to read/write so that get/post requests can be made.

For some reason, the API key is called username in some parts of the documentation (and if I recall correctly, the api password is other times called access token in the error response messages).

#### Admin API resources

The Rest Admin API is organized by resource. Obviously, that means we need to use different APIs to access different resources (the whole shop, products, orders etc.). The APIs are: 

- Access: View access scopes given by the merchant and give out Storefront Access Tokens to give unauthenticated access scopes to clients that need unauthenticated access to the Storefront API.
- Analytics: Gives the merchant reports to analyze business performance.
- Billing: Shows how much the apps cost (if they are paid) and what payment plans they have.
- Customers: Shows customer details that the customers have filled in when making purchases, plus ways to update them.
- Deprecated API calls: Call this endpoint to see calls your app has made in the past 30 days that are now deprecated.
- Discounts: Apply discounts based on price rules you set and check them out.
- **Events**: Shopify resources generate **events** when certain actions are completed (articles, blogs, collections, comments, pages, orders, price rules, products ...). With this API, a merchant can track events for any resources that are important for them. Also, by using **webhooks**, a merchant can set trigger actions when specific events occur in their shop.
In order to create a custom webhook, for example one that tracks when our app is installed/uninstalled, let's check out the fields of Webhooks that we need to set:
    - *address*: URL where the webhook subscription must send the post request when the event occurs
    - *fields*: an optional array of fields that should be included in the subscription
    -format: data format of the subscription (either "json" or "xml")
    - *topic*: event that **triggers** the webhook!

***So, if we wanted to create a webhook for tracking app uninstallations, we should send a post request to the webhooks resource, with the topic being "app/uninstalled" (there doesn't seem to be a topic for apps being installed, but we can make a trigger in our app once someone installs it).***

- **Inventory**: Inventory items are the physical goods available to be shipped to a customer. Every product variant (see more in the product section) has a one to one relationship with an inventory item, and every inventory item can have many locations. An inventory location can have many inventory items for many variants. Between inventory items and their locations, there are intermediate blocks called inventory levels, that represent a certain inventory item's quantity at a certain location. Locations can be a business' headquarters, stores etc.
- Marketing Events
- Metafields: metafields are pieces of data that apps attach to products, customers, orders etc. They can attach additional info that otherwise cannot be placed into other categories, such as instructions for washing and drying for a certain apparel item.
- Online Store: an API that has to do with the merchant's online storefront and its content (articles, pages, blogs, redirects, comments, themes etc.)
- Orders: lets a merchant receive, manage and process their orders (can also show how many checkouts were abandoned, can create orders on behalf of customers, can check risk analysis on orders, create refunds, transactions)
- Plus

- **Products**: The most important API for our development purposes. It is split into a few different resources that we need to list in detail.
    
    - Collection: A collection is a grouping of products that merchants create to make their store easier to browse. There are two categories of collections, **custom collections**, which contain products manually added to a collection and **smart collections**, which contain products automatically added to a collection based on a previously set condition.
    - Collect: This is the resource that connects a product to a collection. For every product there is a collect that tracks collection and product id. Obviously, since a product may be in more than one collection (a frappe mixer could be in a collection called "Kitchen Utilities" and one called "Electronics"), there will be a collect resource for every collection the product belongs to. 
    Smart collections also operate with the collect resource, but cannot be created manually, as with the custom collection, since they are **automatically created** based on the **rules** that the smart collection has set. Smart collection rules can change through the Shopify store UI, but I don't know if you can update the rules through the API (at least it is not included in the documentation).
    - Product: this resource allows you to check, update and create products in a merchant's store. As mentioned above, products can be added to collections. Products may have product variants and different product images.
        **Product endpoints to wrap our heads around**:
        -  *id*: the unique identifier (integer) of a product across the Shopify system
        - *body_html*: description of the product (supports html)
        - *handle*: the slug, a human friendy string for the product
        - *images*: an array of images associated with the product
        - *options*: an array the options of the product that the variants will be based on
        - status: active (ready to sell, available to customers), archived (no longer being sold, unavailable to customers), draft (not yet ready to sell and become available to customers)
        - *tags*: string of comma separated tags (why not array?)
        - *title*: the name of the product
        - *vendor*: the product's supply chain
        - ***variants***: an array that lists out product variants for a product
    - **Product variant**: A variant is added to a product to represent a version of a product with several options. Every product can have a variant for every possible combination of its options. Each product can have max three different options and max 100 variants. Shopify has three default variant options: Size, Color, Material, but merchants can set their own different options. Be careful: variants can be added, updated and deleted, but their quantities cannot be adjusted or set using the variant resource. In order to **update inventory**, we do it through the **inventory levels**. Variants have a 1 on 1 relationship with inventory items.
        **Product variant endpoints to wrap our heads around:**
        - *barcode*: barcode, UPC, ISBN of the product
        - *compare_at_price*: price before an adjustment or sale was applied
        - *price*: the actual price of the variant
        - *presentment_prices*: an array of objects that is used to list out the prices and compare-at prices in each currency, in which every object holds two objects, one for the price and one of the compare-at price
        - *id*: the variant id
        - *product_id*: the product id the variant belongs to
        - *image_id*: the image associated with the variant
        - *inventory_item_id*: the inventory item id associated with the variant
        - *inventory_quantity*: total inventory of variant across all locations (as stated above, there used to be two relative endpoints to show old quantity and to do adjustments, but now we use the inventory level for these operations)
        - *option*: for every variant, there are three option properties (option1, option2, option3), by default these are color, size and material, but the merchant can put their own options too. Option 1, 2 and 3 are not the option category, but the actual option itself (for example, if the variant is a pink, small stone made of onyx, option1 will be pink, option2 will be small and option3 will be onyx)
        - *title*: title of variant, a concatenation of the different options the variant has
        - *sku*: a unique id of the variant in the shop
    - Product images: Just like a product may have many variants, it can also have many images. There is no strict relation with images and variants, but through the product image properties, we can attach an image to different variants. Obviously, an image that is to be associated to a product variant, must also be associated to the **same product**.
- Sales Channel: This API is relevant to all processes that are done through sales channels, which are apps, websites and online marketplaces, not on Shopify. Through this API you can track, create or update checkouts, payments and other resources.
- Shipping & fullfilment: We already know that an order is a customer's completed request to purchase one or more products from a shop, meaning they have completed the checkout process. Every order has a fulfillment resource, which tracks if the items in an order are shipped and received by the customer or not, whether it was cancelled etc. Upon the creation of an order (when a checkout was completed), Shopify decrements the inventory of that variant, but it doesn't yet know which location the item will be fulfilled from (which shop/warehouse the customer will receive the item from), unless the order is placed through Shopify POS. Shopify then decrements the inventory level at the location with the lowest ID.
When the order is actually fulfilled, the fulfillment now includes the fullfillment location id, so Shopify has to check whether it was right with the location it chose to decrement its inventory level. If it wasn't, then it raises the inventory level of the assumed location and lowers the level of the correct location.
- Shopify Payments: Holds info on a merchant's Shopify Payments account (current balance, disputes, payouts and transactions).
- Store properties: Manages a store's configuration (country, currency, province etc.)
- Tender Transactions: Tender transactions represent transactions that modify the shop's balance.

#### API rate limits

The REST Admin API has a rate limit based on requests made per minute. An app therefore has a limit on how many requests it can make on a store at the same time. The standard limit is 40 per minute and on Shopify Plus it is 80.

#### How do we achieve paginated requests?

When a request is made to an endpoint that supports paginated results, we can set our own limit on the results that will be returned (in any case, Shopify by default returns 50 results and maximum 250). Upon the initial request, if there are products that can be retrieved beyond the limit, then the response returns a **Link** header that looks like this:

Link: "<https://{shop}.myshopify.com/admin/api/2019-07/products.json?page_info=hijgklmn&limit=3>; rel=next"

This gives us a url in a parameter called page_info (in this example it is *hijgklmn*) and a rel property that signifies the relevance of the url. In this case, it shows us that it points to the products that follow next.

If we now make a new request and append a page_info parameter that is the url given to us by the Link header, then the next items will be retrieved in the response body. Now, the Link header will look like this:

Link: "<https://{shop}.myshopify.com/admin/api/2019-07/products.json?page_info=abcdefg&limit=3>; rel=previous, <https://{shop}.myshopify.com/admin/api/2019-07/products.json?page_info=opqrstu&limit=3>; rel=next"

Now the header includes links for both the previous page of results and the next. These URLs can be used to iterate through pages. When there is no link included for a next page, that means we have reached the end of the results.

We can change the limit of results through following requests (if I initially made a request with a limit of 3 and want to continue to the next page, I can make a request to it with a limit of 6 if I want).

Pagination URLs are temporary and should be used when working with the requests that generated them in the first place. Never forget that the same authorization will be required on the subsequent requests for the next resources!

#### Using Partners API

**Why do we need this API?**
App Events will trigger custom hooks to send automated emails upon app installation and uninstallation ("Thank you for installing our application etc." / "We recently saw you uninstalled the application, give us some feedback etc.")

The Partners API can only be accessed using GraphQL requests. Unlike REST APIs, which expose a different endpoint for each resource object, a GraphQL API makes all data available at a single endpoint. A client specifies exactly the data that it wants, and the server responds with only that data.
REST APIs work with different types of requests (GET, POST, PUT, DELETE...), while GraphQL works with queries and mutations.

You can make GraphQL requests through the GraphiQL explorer in the partners page, or through Postman, by making a post request to graphql.json, providing the organization id in the url and the X-Shopify-Access-Token as a header, with the code below being an example body (text/plain for the Content-Type header seems to work fine):

    query {
        app(id: "gid://partners/App/5191721") {
            id,
            name
            events (types:[RELATIONSHIP_INSTALLED]) {
                edges {
                    node {
                        type,
                        occurredAt,
                        shop {
                            id,
                            name
                        }
                    }
                }
            }
        }
    }

This query finds all events that match the relationship installed type for the app id we provided, and retrieves the date it occured and the shop id and name it occured on. Basically, it tells us when our application was installed and for which shops. The response will be a JSON that looks like this: 

    {   
        "data": {
            "app": {
                "id": "gid://partners/App/5191721",
                "name": "Testing App",
                "events": {
                    "edges": [
                        {
                            "node": {
                                "type": "RELATIONSHIP_INSTALLED",
                                "occurredAt": "2021-04-21T12:44:42.000000Z",
                                "shop": {
                                    "id": "gid://partners/Shop/56474304703",
                                    "name": "my-stef-store"
                                }
                            }
                        }
                    ]
                }
            }
        }
    }

**What the heck are edges and nodes supposed to be?** Well, in Shopify's GraphQL API, everything is visually represented in the form of a graph. Graphs contain nodes that are interconnected through edges, for example Products is a root node that is connected through edges to several different Product nodes.




### A view on the Storefront API