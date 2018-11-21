# @shopify/theme-addresses

theme-addresses.js helps developers work with common address releated operations, such as, building the country and province selectors

## Getting Started

Theme Scripts can be used in any theme project. To take advantage of semantic versioning and easy updates, we recommend using NPM or Yarn to include them in your project:

```
yarn add @shopify/theme-addresses
```

and then import the functions you wish to use through ES6 imports:

```
import {CountryProvinceSelector} from '@shopify/theme-addresses`;
```

If you prefer not to use a package manager, you can download the latest version of Theme Addresses and include it in your project manually from the following links:

- [theme-addresses.js](http://unpkg.com/@shopify/theme-addresses@latest/dist/theme-addresses.js)
- [theme-addresses.min.js](http://unpkg.com/@shopify/theme-addresses@latest/dist/theme-addresses.min.js)

## Methods

### CountryProvinceSelector(countryOptions)

Prepares an instance of a `CountryProvinceSelector` with the given country options

- `countryOptions`: This is an html string generated by the following liquid tags:
    * [`{{ all_country_option_tags | json }}`](https://help.shopify.com/en/themes/liquid/objects/all-country-option-tags)
    * [`{{ country_option_tags | json }}`](https://help.shopify.com/en/themes/liquid/objects/country-option-tags)

Example
```html
// In theme.liquid, somewhere before the end of </head> tag
    ...
    {% if template.directory == 'customers' %}
      theme.allCountryOptionTags = {{ all_country_option_tags | json }};
    {% endif %}
  </script>
  ...
</head>
```
```js
// In addresses.js
import {CountryProvinceSelector} from '@shopify/theme-addresses';

const billingCountryProvinceSelector = new CountryProvinceSelector(window.theme.allCountryOptionTags);
```

---

### build(countryNodeElement, provinceNodeElement, [options](#options))

Populates the given `<select>` elements with country and province options.

- `countryNodeElement`: The `<select>` node element for countries
- `provinceNodeElement`: The `<select>` node element for provinces according to the selected country
- `options`:
    * `onCountryChange`: The callback after a country `change` event
    * `onProvinceChange`: The callback after a province `change` event

The Select node element should look like the following:
```html
    <select ... id="newCountry" name="address[country]" data-default="{{ form.country }}"></select>
    <select ... id="newProvince" name="address[province]" data-default="{{ form.province }}"></select>
```

Example
```js
import {CountryProvinceSelector} from '@shopify/theme-addresses';

const billingCountryProvinceSelector = new CountryProvinceSelector(window.theme.allCountryOptionTags);

billingCountryProvinceSelector.build(document.querySelector('#newCountry'), document.querySelector('#newProvince'));
billingCountryProvinceSelector.build(document.querySelector('#editCountry'), document.querySelector('#editProvince'));
```

We do not dictate what selector library you can use in your themes as long as the element being passed in is a native Node element.

Example using jQuery
```js
import $ from 'jquery';
import {CountryProvinceSelector} from '@shopify/theme-addresses';

const billingCountryProvinceSelector = new CountryProvinceSelector(window.theme.allCountryOptionTags);

billingCountryProvinceSelector.build($('#newCountry')[0], $('#newProvince')[0]);
billingCountryProvinceSelector.build($('#editCountry')[0], $('#editProvince')[0]);
```

#### options

##### onCountryChange(provinces, provinceNodeElement, countryNodeElement)

The callback when a `change` event happens on the country select.

- `provinces`: The array of provinces of selected country
- `provinceNodeElement`: The `<select>` node element for provinces
- `countryNodeElement`: The `<select>` node element for countries

##### onProvinceChange(event)

The callback when a `change` event happens on the province select.

- `event`: The `change` event object of the province select

Example

```html
    <select ... id="newCountry" name="address[country]" data-default="{{ form.country }}"></select>
    <div id="newProvinceContainer">
        <p>Provinces</p>
        <select ... id="newProvince" name="address[province]" data-default="{{ form.province }}"></select>
    </div>
```

```js
import {CountryProvinceSelector} from '@shopify/theme-addresses';

const newProvinceContainer = document.querySelector('#newProvinceContainer')
const billingCountryProvinceSelector = new CountryProvinceSelector(window.theme.allCountryOptionTags);

billingCountryProvinceSelector.build(
    document.querySelector('#newCountry'),
    document.querySelector('#newProvince'),
    {
        onCountryChange: (provinces) => {
            if (provinces.length) {
                newProvinceContainer.classList.remove('hide');
            } else {
                newProvinceContainer.classList.add('hide');
            }
        }
    });
```