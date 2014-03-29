# Angular.js templates wrapper for SocketStream framework

**ss-angular-templates** - is a wrapper for [angular.js](http://angularjs.org) templates, provides server-side compiled templates (html/jade) for [SocketStream](http://socketstream.org) applications.

```$ npm install ss-angular-templates --save```

# Why
Let's say we have next templates structure:
```
client/
    templates/
        libs/
            template/
                accordion/
                    accordion-group.html
                    accordion.html
        /pages
            index.jade
            about.jade
```


...in most cases we need to have an access within an [angular.js](http://angularjs.org) application to the templates with [templateUrl](http://docs.angularjs.org/api/ngRoute.$route):
```JavaScript
...
templateUrl: 'template/accordion/accordion.html', // in case if you are using http://angular-ui.github.io/bootstrap/
templateUrl: 'template/accordion/accordion-group.html',
templateUrl: 'pages/index.html', // Jade -> HTML convertation
templateUrl: 'pages/about.html',
...
```
But SocketStream's [default angular.js template wrapper](https://github.com/socketstream/socketstream/blob/master/lib/client/template_engines/angular.js) wraps templates with attribute ```id``` like this:

```HTML
    <script id="tmpl-libs-template-accordion-accordion.html" type="text/ng-template">
    <script id="tmpl-libs-template-accordion-accordion-group.html" type="text/ng-template">
    <script id="tmpl-pages-index.html" type="text/ng-template">
    <script id="tmpl-pages-about.html" type="text/ng-template">
```

In addition, default `angular.js` templates wrapper doesn't support [Jade tamplate engine](http://jade-lang.com/) files.



# Solution
Since SocketStream is so awesome that provides [the opportunity to write custom client](https://github.com/socketstream/socketstream/blob/master/doc/guide/en/template_engine_wrappers.md) wrappers you can use **ss-angular-templates** to be comfortable with Angular.js templates.

Include this line in your server-side app.js:
```JavaScript
    ss.client.templateEngine.use(require('ss-angular-templates'), '', {idTransformer: customIdTransformer});
```
where `idTransformer` is a function for `id` template's attribute transformer:
```JavaScript
    function customIdTransformer(template, path, id) {
        return 'custom-id-string';
    }
```


You can also use this module for [Jade tamplate engine](http://jade-lang.com/) files, here is a real live example:

```JavaScript
    /**
     * Another Custom transformer function for angular Id templates
     * @param  {String} template Compile from Jade file HTML string
     * @param  {String} path     Jade templade file path
     * @param  {String} id       Default suggested 'id' by SocketStream
     * @return {String}          Transformed template with id for '<script type="text/ng-template" id="' + id + '.html">' + template + '</script>'
     */
    function jadeTemplateTransformer(template, path, id) {
        /*
         *  Path
         *      'path-to-your-project/client/templates/pages/about.jade'
         *  will be transformed into
         *      'pages/about.html'
         *
         */
        return path.replace( pathlib.join(process.env.PWD, '/client/templates/'), '').replace('.jade', '.html');
    }

// Use 'ss-angular-templates' wrapper for all the Jade file inside '/pageElements' (relevant to 'client/templates') with custom 'id' transformation function 'jadeTemplateTransformer()'
ss.client.templateEngine.use(require('ss-angular-templates'), '/pageElements', {jade: true, idTransformer: jadeTemplateTransformer});
```

# Options
- **idTransformer** {Function} - `id` template's attribute transformer
- **jade** {Boolean} - allows to compile Jade template engine files into HTML string

