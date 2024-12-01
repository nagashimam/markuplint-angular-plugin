# Angular Template Plugin for Markuplint 

This plugin extends [Markuplint](https://markuplint.dev/) to provide support for Angular templates. Angular templates introduce unique challenges for static analysis, such as syntax that is not valid as HTML and dynamic expressions that depend on runtime evaluation. This plugin helps ensure your Angular templates are linted effectively with Markuplint. 

## Contributing 

Contributions are welcome! If you have questions or feature requests, feel free to open issues in English or Japanese. 

ご質問や機能リクエストがございましたら、英語または日本語でお気軽にIssueを作成してください。 


## Key Features 

1. **Support for Modern Angular Control Flow Syntax**
2. **Validating All Branches in Built-in Control Flow** 

### Support for Modern Angular Control Flow Syntax

This plugin supports Angular's new built-in control flow syntax such as `@if` and `@for`. These modern constructs replace the older structural directives (`*ngIf`, `*ngFor`, etc.) and are fully supported by the plugin for linting. 

Angular block syntax is not valid as pure HTML, so simply running Markuplint against Angular templates can cause false positive errors. For example, running Markuplint against the following template: 

```html
@if (items.length > 0) {
  <ul>
    @for (item of items; track item) {
      <li>{{ item }}</li>
    }
  </ul>
}
``` 

produces the error: `The text node is not allowed in the "ul" element in this context permitted-contents`. 

However, this is a false positive because this template is compiled into valid HTML. By parsing the template, this plugin ensures that such errors do not occur. 

### Validating All Branches in Built-in Control Flow

Angular's template syntax can also hide problems, leading to false negatives. For instance, consider the following template: 

```html 
<dl>
  <dt>Authors</dt>
  @for (author of authors; track author) {
    <dt>authors</dt>
    <dd>{{ author }}</dd>
  }
</dl>
```

If `authors` is an empty array, the `<dl>` element is alos empty, which is invalid as HTML. Without parsing all branches, this issue would go undetected. To lint these templates correctly, this plugin parses the Angular syntax, evaluates all possible branches, and validates each scenario, ensuring comprehensive linting. 

## Note on Structural Directives 

This plugin **does not support** older structural directives like `*ngIf` and `*ngFor`. It is designed specifically for Angular's new built-in control flow syntax. If you are using the older syntax, you'll need to migrate your templates to take full advantage of this plugin. 


## How It Works 

### Handling `@if` Blocks 

When this plugin encounters an `@if` block, it evaluates all possible branches and validates each resulting structure. For the following template: 
```HTML
@if (flagA) {
  <p>A is true</p>
} @else if (flagB) {
  <p>B is true</p>
} @else {
  <p>Both A and B are false</p>
}
``` 

This plugin lints: 

```html
<p>A is true</p>
``` 
, 

```HTML
<p>B is true</p>
``` 
, and 
```html
<p>Both A and B are false</p>
```

### Handling `@switch` Blocks 

Similarly, this plugin evaluates all cases in a `@switch` block. For the following template: 

```html 
@switch (str) {
  @case ("foo") {
    <p>str says foo</p>
  }
  @case ("bar") {
    <p>str says bar</p>
  }
  @default {
    <p>str says something else</p>
  }
}
```

This plugin lints: 

```html
<p>str says foo</p>
```
,
```html
<p>str says bar</p>
```
, and
```html
<p>str says something else</p>
``` 

### Handling `@for` Loops 

When this plugin encounters an `@for` loop, it evaluates the template for typical scenarios: 

1. The loop does not execute at all. 
2. The loop executes once. 
3. The loop executes twice. 

For the following template: 

```html
<ul>
  @for (item of items; track item) {
    <li>{{ item }}</li>
  }
</ul>
```

This plugin lints: 

```html
<ul></ul>
```
,
```html
<ul>
  <li>{{ item }}</li>
</ul>
```
, and
```html
<ul>
  <li>{{ item }}</li>
  <li>{{ item }}</li>
</ul>
```

## Installation(WIP)

To use this plugin, first install Markuplint and the Angular Template Plugin: 
```bash
npm install --save-dev markuplint @your-username/markuplint-angular-plugin 
``` 

## Usage(WIP)

Add the plugin to your Markuplint configuration file (such as `.markuplintrc` or `markuplint.config.js`): 

```json 
{
    "extends": [],
    "plugins": [
        "@your-username/markuplint-angular-plugin"
    ],
    "parser": {
        ".component.html": "@your-username/markuplint-angular-plugin"
    }
}
``` 

You can then lint your Angular templates like any other file: 

```bash 
markuplint src/app/**/*.html 
```

## License

This plugin is licensed under the [MIT License](LICENSE). 
