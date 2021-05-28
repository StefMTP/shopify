## Design

### Typography
Formatting: left aligned, use strong rarely, underlined reserved for links, subdued (greyed out-muted text) indicates subtext beneath text, deemphasized or less important content

#### Font sizes for text in different contexts
Display x-large - Displays are reserved for titling modals, empty states etc.
Display large
Display medium
Display small
PageHeading - title of a screen, an entire view/page
Heading - title of a top level section of the screen (such as a card title)
SubHeading - if there are subsections within a section, use this to title them
Button - self explanatory
Body - self explanatory
Caption - compact details in tight spaces

### Interaction States
Default - Hovered - Focused - Pressed - Disabled

Other useful components and properties that indicate feedback of interaction:
Progress bar/spinner - toast - validation errors and text fields

### Spacing
In incrementals of 4 pixels (basic unit of spacing measurement)
Common components that include this spacing behaviour: text container, stack, buttons and button groups, form layout, card, layout

### Colors

#### Basic color roles 
- light grey = surface #F0F0F0
- dark = on surface #111213
- shopify green = primary color #008060
- grey = secondary #CCCCCC
- blue = interactive #2E72D2
- red = critical #D82C0D
- yellow = warning #FFC453
- other green = success #008060
- cyan = highlight #5BCDDA
- other colors = decorative

The are also variants for colors, and their values are based mainly on the color role. Their naming pattern is based on the color role, the ui element they are linked to and the interaction state they define (e.g. Card primary subdued).

#### Roles in the Shopify applications
- Surface colors affect component surfaces (cards, popups, pages etc.)
- On Surface affects text, borders, some secondary icons
- Primary colors are used for primary actions (buttons, icons, tabs for navigation etc.)
- Secondary is used for secondary-tertiary buttons, form actions
- Interactive is used for links, focus indicators, selected elements
Success is used for positive actions and messages
- Warning is used to notify the merchant something is missing, further actions must be taken
- Critical indicates errors, danger, something must be done immediately to fix the problem
- Highlight is used for information that doesn't require immediate action

### Icons
Use these visual helpers sparingly and minimally, often paired with descriptive text.
Common usage: navigation, along with headers and titles, in banner messages, inline with text, or whenever they will help attract attention to an action the merchant can take.

## Authentication

In Shopify, apps that are embedded on Shopify admin do not rely on third party cookies to store sensitive merchant info. Instead, it uses session tokens to make requests to the backend. The backend uses this token to determine the user's identity.

Session tokens go hand to hand with OAuth, because the backend still needs to have an API Access Token to make requests to Shopify's APIs.

### Session token auth flow

- The Shopify admin requests to load the app from the backend
- Since the app is unauthenticated yet, the backend loads the skeleton of the app
- The skeleton is used to create an App Bridge client on the frontend
- The frontend requests the session token from App Bridge, and the latter returns it
- The frontend, in order to request data from the backend, needs to pass in the session token (as an authorization header)
- The backend verifies the signature of the session token and if it's correct, it returns data to the frontend

The two last steps are repeated whenever the frontend requires data from the backend, once the first steps of the process are complete. A crucial thing to remember: the session token expires after a minute. Every time a session token needs to be used for a request, it must be fetched using App Bridge.

### Session token request flow

The request is sent with the session token. Is the session token invalid, or expired? Then the request must be rejected. If not, then does the app have an access token for the shop making the request? If not, we must go through OAuth. Otherwise, the backend responds to the request immediately.

The session token is a standard JSON Web Token that is comprised of three fields: 

- The header, which describes the encoding algorithm used to encode the token and the type of the token
- The payload, which carries the info passed with the session token. Its fields are iss (the shop's admin domain), dest (the shop's domain), aud (the api key of the receiving app), sub (which user the token is intended for), exp (token expiration date), nbf (token activation date), iat (when token was issued), jti (secure random uuid for the token) and sid (unique session id for user and app). All dates are timestamps (seconds from the UNIX epoch).

### App Bridge

The App Bridge is used to load the embedded app, fetch the session tokens, make redirects and other actions, make requests to the backend for data.

The response we send to Shopify must have a Content-Security-Policy header set to frame-ancestors 'self' {shop-name}.

The initial OAuth redirect for authorization must occur at parent level, outside the iframe the app is loaded inside. Depending on what the current 'window' is, we redirect the user differently, but they still see the install permission prompt.

The shopOrigin value is required to lock communication with just the one shop currently using the application. It is the encoded value of the shop name, that we can manually extract from the installation confirmation url of the authorization process. App Bridge requires this shop value, so it is best to be stored for the duration of the user session (Could be stored in an Adonis session key-value pair as a http only cookie).

After the auth flow, the user will end up at the redirect url (recommended to let App Bridge redirect the user back to Shopify to ensure the app is embedded in the admin).

