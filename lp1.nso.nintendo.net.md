# nso-applet-api

Prepend all routes with https://lp1.nso.nintendo.net.  
All requests are made with HTTP2.

| Name | Implements | Method |
| --- | --- | --- |
| [NSOAppletAPI.getV1Cookies()](#getv1cookies) | [/api/v1/cookies](https://lp1.nso.nintendo.net/api/v1/cookies) | **GET** |
| [NSOAppletAPI.getV1LClassicsTitles()](#getv1lclassicstitles) | [/api/v1/classic_games](https://lp1.nso.nintendo.net/api/v1/classic_games) | **GET** |
| [NSOAppletAPI.getV1GiftCategories()](#getv1giftcategories) | [/api/v1/gift_categories](https://lp1.nso.nintendo.net/api/v1/gift_categories) | **GET** |

## getV1Cookies
Stores the following cookies: `CloudFront-Key-Pair-Id`, `CloudFront-Policy`, `CloudFront-Signature`.  
They are returned in the `Set-Cookie` header of the response. `NSOAppletAPI.getV1Cookies()` only returns a [Cookie](#cookie) object.

### Request

| Parameter | Type | Description |
| --- | --- | --- |
| country | str | the account's country code |

### Response

| Type | Description |
| --- | --- |
| [Cookie](#cookie) | an object that contains the expiry information |

## getV1LClassicsTitles
Gets the current titles available under NSO's emulation softwares.

### Request

| Parameter | Type | Description |
| --- | --- | --- |
| statuses | str | correlates to statuses[] - commonly is `published` |
| country | str | the account's country code |

### Response

| Type | Description |
| --- | --- |
| list<[Classic_Game](#classic_game)> | a list of objects containing all NSO games available under NSO's emulation softwares |

## getV1GiftCategories

### Request


### Response

# Objects

| Name | Origin |
| --- | --- |
| [Cookie](#cookie) | [nso](/nso/structures.py) |
| [Classic_Game](#classic_game) | [nso](/nso/structures.py) |
| [Bundled_Region](#bundled_region) | [nso](/nso/structures.py) |

## Cookie

An object with the following:

| Name | Type | Description |
| --- | --- | --- |
| expires | int | UNIX timestamp with cookie expiry date |

## Classic_Game

An object with the following:

| Name | Type | Description |
| --- | --- | --- |
| status | str | Seems to always be `publishing` |
| title_id | str | Title IDs for respective console |
| title_name | str | Name of the game |
| application_id | str | Matches an NSO console emulator title ID |
| application_type | str | Console type description |
| bundled_region | [Bundled_Region](#bundled_region) | Includes special region information. Commonly, this is `None` |
| icon_url | str | Game's icon URL |
| publisher | str | Game's publisher |
| is_unknown_release_date | bool | Whether the release date is known or not |
| released_at | str | When game was initially released |
| published_at | str | When game was added to NSO |

## Bundled_Region

| Name | Type | Description |
| --- | --- | --- |
| region | str | Region code (i.e. `EUR`, `KR`, etc) |
| languages | list | List of language codes. Can be `None` |

# Testing the NSO Webapplet with dev tools

<details>
  <summary><b>Show basic tutorial</b></summary

  Some quick documentation on getting the NSO webapplet in your browser (from information discovered by Samuel Elliot):
  ```
  https://accounts.nintendo.com/connect/1.0.0/authorize?client_id=f4e5f2f3e022208b&response_type=id_token&scope=openid&redirect_uri=nintendo://lhub.nx.sys&state=a
  ```
  Get id_token from the above.

  Go to <https://lp1.nso.nintendo.net> -- set your user agent to something like `Mozilla/5.0 (Nintendo Switch; NsoApplet; Nintendo Switch) AppleWebKit/609.4 (KHTML, like Gecko) NF/6.0.2.22.5 NintendoBrowser/5.1.0.23519`

  Add breakpoints to the index's script at `var n = navigator.userAgent.includes("NintendoBrowser");` and the static/js/main.1d72eb58.js at `setNaAuthTokenAvailableCallback: function(e) {e(void 0, void 0, EP.b)},`

  Reload page and, on first breakpoint, paste the below
  ```
  Object.defineProperty(navigator, 'userAgent', {get: () => 'no'})
  ```
  Then resume and on second breakpoint, paste the below, replacing "id_token" with your id_token from before
  ```
  e(undefined, undefined, 'id_token')
  ```

  If you'd like a bunch of info dump, make a breakpoint anywhere with `r.getState()`, then run `r.getState();` in the Console whenever it breaks there.

</details>
