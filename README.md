<p align="left">
  <img src="https://github.com/gurindersingh/vue-datepicker/blob/master/static/assets/logo.png" width="150"/>
</p> 

# Vue.js Datepicker
> Bootstrap compatible (v3.3.7) & Touch enabled - Vue.js Datepicker 

<p align="left">
  <img src="https://github.com/gurindersingh/vue-datepicker/blob/master/static/assets/screen_1.jpeg" width="300"/>
</p>

## Requirements
- [Vue.js](https://github.com/vuejs/vue) `^2.4.*`
- [moment](https://github.com/moment/moment) `^2.18.*`

## Usage
```html
<vue-datepicker
    name="input name"
    placeholder="placeholder"
    value="'YYYY-MM-DD HH:mm:ss'"
    :format="'YYYY-MM-DD HH:mm:ss'"
    :min="'YYYY-MM-DD HH:mm:ss'"
    :max="'YYYY-MM-DD HH:mm:ss'"
    :time="true"
    :readonly="true"
    :required="false"
    :validate="true"
></vue-datepicker>
```

## Props
Name | Type | Default | Description
---|:---|:---:|---
`name` | `String` | `Laravel Plus Datepicker` | Name of the input 
`placeholder` | `String` | `Pick a date...` | Placeholder text 
`:value` | `String` | `null` | Default value 
`:format` | `String` | `YYYY-MM-DD HH:mm:ss` | Output format for the date
`:min` | `String` | `YYYY-MM-DD HH:mm:ss` | Min date to allow for select 
`:max` | `String` | `YYYY-MM-DD HH:mm:ss` | Max date to allow for select 
`:time` | `Boolean` | `true` | Enable time selection 
`:readonly` | `Boolean` | `false` | Make input read only 
`:required` | `Boolean` | `false` | HTML5 required attribute 
`:validate` | `Boolean` | `false` | Enable validation 


## License
[The MIT License](http://opensource.org/licenses/MIT)
