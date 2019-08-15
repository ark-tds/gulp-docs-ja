# Swing と YAML front-matter を用いたテンプレート
テンプレートは、`gulp-swig` と `gulp-front-matter` を使用してセットアップできます。

##### `page.html`

```html
---
title: Things to do
todos:
    - First todo
    - Another todo item
    - A third todo item
---
<html>
    <head>
        <title>{{ title }}</title>
    </head>
    <body>
        <h1>{{ title }}</h1>
        <ul>{% for todo in todos %}
          <li>{{ todo }}</li>
        {% endfor %}</ul>
    </body>
</html>
```

##### `gulpfile.js`

```js
var gulp = require('gulp');
var swig = require('gulp-swig');
var frontMatter = require('gulp-front-matter');

gulp.task('compile-page', function() {
  gulp.src('page.html')
      .pipe(frontMatter({ property: 'data' }))
      .pipe(swig())
      .pipe(gulp.dest('build'));
});

gulp.task('default', ['compile-page']);
```
