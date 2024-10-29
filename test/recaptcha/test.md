# way to add recaptcha without pakage 

### in env 
```php

RECAPTCHA_SITE_KEY=hhhhhhh
RECAPTCHA_SECRET_KEY=hhhhh


```

### in the form we should add 
```php

<script src="https://www.google.com/recaptcha/api.js" async defer></script>
<div class="g-recaptcha" data-sitekey="{{ env('RECAPTCHA_SITE_KEY') }}"></div>

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
            ['secret' => env('RECAPTCHA_SECRET_KEY'), 'response' => $gResponseToken]
        );

        if (!json_decode($response->body(), true)['success']) {
            $fail('Invalid recaptcha');
        }
    }
}

```

thanks,
mahmoud hafez 🥰