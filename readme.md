imagetyperz-api-php - Imagetyperz API wrapper
=========================================

imagetyperzapi is a super easy to use bypass captcha API wrapper for imagetyperz.com captcha service

## Installation

    composer require imagetyperzapi/imagetyperzapi

or
    
    git clone https://github.com/imagetyperz-api/imagetyperz-api-php

## How to use?

Simply require the module, set the auth details and start using the captcha service:

``` php
require('lib/imagetyperzapi.php');      // load API library
```

Set access_token or username and password (legacy) for authentication
``` php
// get access token from: http://www.imagetyperz.com/Forms/ClientHome.aspx
$access_token = 'your_access_token_here';
$i = new ImagetyperzAPI($access_token);   
```

Legacy way, will get deprecated at some point

``` php
$i->set_user_password('your_user', 'your_password');  
```

Once you've set your authentication details, you can start using the API

**Get balance**

``` php
$balance = $i->account_balance();
```

**Submit image captcha**

``` php
// check example for optional_parameters array
$captcha_text = $i->solve_captcha('captcha.jpg', optional_parameters);
```

**Works with URL instead of image file but only if authenticated with token**
``` php
$captcha_text = $i->solve_captcha('http://scurt.pro/captcha.jpg');
```
**Submit recaptcha details**

For recaptcha submission there are two things that are required.
- page_url
- site_key
- type - can be one of this 3 values: `1` - normal, `2` - invisible, `3` - v3 (it's optional, defaults to `1`)
- v3_min_score - minimum score to target for v3 recaptcha `- optional`
- v3_action - action parameter to use for v3 recaptcha `- optional`
- proxy - proxy to use when solving recaptcha, eg. `12.34.56.78:1234` or `12.34.56.78:1234:user:password` `- optional`
- user_agent - useragent to use when solve recaptcha `- optional` 
``` php
$params = array();
$params['page_url'] = 'page_url_here';
$params['sitekey'] = 'sitekey_here';
$params['type'] = 3;    // optional
$params['v3_min_score'] = 0.3;          // min score to target when solving v3 - optional
$params['v3_action'] = 'homepage';      // action to use when solving v3 - optional
$params['proxy'] = '126.45.34.53:123';  // - optional
$params['user_agent'] = 'Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0'; // optional
$captcha_id = $i->submit_recaptcha($params);
```
This method returns a captchaID. This ID will be used next, to retrieve the g-response, once workers have 
completed the captcha. This takes somewhere between 10-80 seconds.

**Retrieve captcha response**

Once you have the captchaID, you check for it's progress, and later on retrieve the gresponse.

The ***in_progress($captcha_id)*** method will tell you if captcha is still being decoded by workers.
Once it's no longer in progress, you can retrieve the gresponse with ***retrieve_recaptcha($captcha_id)***  

```php
while ($i->in_progress($captcha_id))
    sleep(10);
// completed at this point
$recaptcha_response = $i->retrieve_recaptcha($captcha_id);
```

## Capy

This captcha requires a `page_url` and `sitekey` in order to be solved by our system.
Currently, in order to solve a capy captcha, you'll have to use the reCAPTCHA methods and only add `--capy` at the end of the `page_url`.
Having that up, our system will pick it up as capy. Once workers have solved it, you'll have to use the reCAPTCHA retrieve endpoint, to get the response.

**E.g** Original page url - `https://mysite.com`, capy page url `https://mysite.com--capy`

## Other methods/variables

**Affiliate id**

The constructor accepts a 2nd parameter, as the affiliate id. 
``` php
$i = new ImagetypersAPI($access_token, 123);
```

**Requests timeout**

As a 3rd parameter in the constructor, you can specify a timeout for the requests (in seconds)
``` php
$i = new ImagetypersAPI($access_token, 123, 60);
```

**Get details of proxy for recaptcha**

In case you submitted the recaptcha with proxy, you can check the status of the proxy, if it was used or not,
and if not, what the reason was with the following:

``` php
echo $i->was_proxy_used($captcha_id);
```

**Set captcha bad**

When a captcha was solved wrong by our workers, you can notify the server with it's ID,
so we know something went wrong.

``` php
$i->set_captcha_bad($captcha_id); 
```

## Examples
Check example.php

## License
API library is licensed under the MIT License

## More information
More details about the server-side API can be found [here](http://imagetyperz.com)


<sup><sub>captcha, bypasscaptcha, decaptcher, decaptcha, 2captcha, deathbycaptcha, anticaptcha, 
bypassrecaptchav2, bypassnocaptcharecaptcha, bypassinvisiblerecaptcha, captchaservicesforrecaptchav2, 
recaptchav2captchasolver, googlerecaptchasolver, recaptchasolverpython, recaptchabypassscript</sup></sub>
