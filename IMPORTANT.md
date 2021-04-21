## Working with Shopify: Developing Apps and Interacting with its APIs

### Concepts and terms to know before starting:

Ecommerce = buying and selling online
Merchant = someone who sells items/services
Online store = the ecommerce website of a merchant
Store = a retail establishment for selling to customers
Collections = products grouped together into categories
Variant = products come in different options in a lot of stores, for example COLOR, or SIZE. That is why, merchants can add variants to a product to distinguish it. Each product may come with different combinations of various options. Each combination of options is a variant of each respective product.
Inventory = a store's complete list of VARIANTS
Item = one unit of a product (if there are variants, an item is one unit of a variant)

#### Using the Shopify CLI

The CLI offers an easy way to automate Shopify app creation and setting up.
Xcode - Homebrew - Shopify and ngrok will be useful, even though most things will be settled through the Shopify CLI.
Shopify CLI for Node comes with some commands that can help us test our app, such as populating our store with example products, customers, orders.

### Inside the App

After we've managed to create the app and embed it to our development store, we can start looking at various different things. First of all, we have Next JS which handles the server side rendering and builds our pages using React for the app's frontend. Polaris is a CSS framework that we can use to create a Shopify app that is appropriately themed to fit Shopify's online environment. It comes with its own design system and provides guidelines for the content and how it should be shaped to have a place in the Shopify ecosystem.

Useful points extracted from developing Shopify store apps: embedded apps and the api bridge, ngrok for tunnelling, shopify CLI making some very complicated stuff a little bit more bearable

### Making API calls to an App

Making sure we have enabled the creation and testing through PRIVATE APPS, we create a new one and copy the api key and token that is given to us. Through Postman, we make requests on our shop apps like this: {api-key}:{api-token}@{store-url}.admin/api/{api-version}/{api-endpoint}

###