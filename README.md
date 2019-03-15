# Accessories

![npm license](https://img.shields.io/npm/l/@enso-ui/accessories.svg) 
![npm download](https://img.shields.io/npm/dm/@enso-ui/accessories.svg) 
![GitHub top language](https://img.shields.io/github/languages/top/enso-ui/accessories.svg) 
![GitHub issues](https://img.shields.io/github/issues/enso-ui/accessories.svg) 
![npm version](https://img.shields.io/npm/v/@enso-ui/accessories.svg) 

Accessories

## Usage

This package contains a suite of reusable components within the Enso ecosystem.

For live examples and demos, you may visit [laravel-enso.com](https://www.laravel-enso.com)

Check the full [documentation](https://docs.laravel-enso.com/frontEnd/accessories.html).

### Installation

Install the package:
```
yarn add @enso-ui/accessories
```

Import the desired component(s):
```js
import { Accessories, Addresses, Comments } from '@enso-ui/accessories/bulma';
```

### Example

```js
<accessories>
    <template slot-scope="{ count }">
        <tab keep-alive
            id="Addresses">
            <addresses :id="myCompanyId"
                type="LaravelEnso\Companies\app\Models\Company"
                @update="$set(count, 'Addresses', $refs.addresses.count)"
                ref="addresses"/>
            <comments :id="myCompanyId"
                type="LaravelEnso\Companies\app\Models\Company"
                @update="$set(count, 'Comments', $refs.comments.count)"
                ref="comments"/>
        </tab>
    </template>
</accessories>
``` 

## Contributions

are welcome. Pull requests are great, but issues are good too.

Thank you to all the people who already contributed to Enso!

## License

[ISC](https://opensource.org/licenses/ISC)
