# way to add recaptcha without pakage 
this link https://www.google.com/recaptcha/admin#list
### in env 
```php

RECAPTCHA_SITE_KEY=hhhhhhh
RECAPTCHA_SECRET_KEY=hhhhh


```

### in config/app.php 
- we add this in the return array.
```php

    'RECAPTCHA_SITE_KEY'=>env('RECAPTCHA_SITE_KEY'),
    'RECAPTCHA_SECRET_KEY'=>env('RECAPTCHA_SECRET_KEY')



```

### in the form we should add 
```php

<script src="https://www.google.com/recaptcha/api.js" async defer></script>
<div class="g-recaptcha" data-sitekey="{{ config('app.RECAPTCHA_SITE_KEY') }}"></div>

```

### in the request validation 
```php

    public function rules()
    {
        return [
            ...,
            ...,
            'g-recaptcha-response' => ['required', new Recaptcha,'exclude'],
        ];
    }
```

## then we must add rule for the Recaptcha 

 ```php artisan make:rule Recaptcha```


 ### in  Recaptcha Rule 
 - v 10
```php

<?php

namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\ValidationRule;
use Illuminate\Support\Facades\Http;

class Recaptcha implements ValidationRule
{
    /**
     * Run the validation rule.
     *
     * @param  \Closure(string): \Illuminate\Translation\PotentiallyTranslatedString  $fail
     */
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        $gResponseToken = (string) $value;

        $response = Http::asForm()->post(
            'https://www.google.com/recaptcha/api/siteverify',
            [
                'secret' => config('app.RECAPTCHA_SECRET_KEY'), 
                'response' => $gResponseToken
            ]
        );

        if (!json_decode($response->body(), true)['success']) {
            $fail('Invalid recaptcha');
        }
    }
}

```

- v 8
```php

<?php

namespace App\Rules;

use Closure;
use Illuminate\Contracts\Validation\Rule;
use Illuminate\Support\Facades\Http;

class Recaptcha implements Rule
{
    /**
     * Run the validation rule.
     *
     * @param  string  $attribute
     * @param  mixed  $value
     * @param  Closure  $fail
     * @return void
     */
    public function passes($attribute, $value): bool
    {
        $gResponseToken = (string) $value;

        $response = Http::asForm()->post(
            'https://www.google.com/recaptcha/api/siteverify',
            [
                'secret' => config('app.RECAPTCHA_SECRET_KEY'), // Ensure this key exists in your config
                'response' => $gResponseToken,
            ]
        );

        return json_decode($response->body(), true)['success'] ?? false;
    }

    /**
     * Get the validation error message.
     *
     * @return string
     */
    public function message(): string
    {
        return 'Please verify that you are not a robot.';
    }
}
```
thanks,
mahmoud hafez 🥰
