Shipday Standalone ES6 Library
========

The idea is develop a es6 library, bundle it as standalone module and use it as a library in new or existing legacy frontend project that will run in browser.

[shipday-lib](/shipday-lib) is the library project and [example](/example) is an example of how this library can be
used in frontend project.

Technical details of shipday-lib
------

`shipday-lib` project required on the following:-

- Node version 14+
- babel 7
- gulp 4

It also usages `mocha` as test runner and `chai` as assertion library.

### setup after cloning
Once this repository is cloned, move to `shipday-lib` directory and run `npm install`

### test task

Here is the gulp task for test:-
```
gulp.task('test', () => {
        return gulp.src('./src/test/**/**.js', {read: false})
            .pipe(mocha({
                require: ['@babel/register'],
                reporter: 'spec'
            }));
    });
``` 

So, it look all the javascript files under [src/test](shipday-lib/src/test) and run
the tests. `@babel/register` is used as compiler. to run this task we also need to
set `@babel/preset-env` as preset of babel in [.babelrc](shipday-lib/.babelrc) file.

for running the test task, just execute the following in terminal

```
./test.sh
```

### task bundle

Here is the gulp task for bundle:-
```
gulp.task('bundle', async () => {
        await browserify({
            entries: ['./src/main/app.js'],
            standalone: 'ShipdayDispatch'
        }).transform(babelify.configure({presets: ["@babel/env"]}))
            .bundle().on('error', err => console.log(err))
            .pipe(source('shipday-lib.js'))
            .pipe(gulp.dest('./artifact'));
    });
```

So, it start bundling from the [app.js](/shipday-lib/src/main/app.js) file. The important thing to notice
that we use `standalone: 'ShipdayDispatch'` in the browserfiy configuration. That will allow us to use the
functionality of this library in external js file and they are accessible in `ShipdayDispatch`

All the es6 code is transpiled to es5 and bundle into `shipday-lib.js` file under `artifact` directory

### task build

build task is just the combination of `test` and `bundle` task in sequence. It will first run the test task.
If all test passes, then proceed to the bundle task which will generate the newer version of `shipday-lib.js` file.

To run the build task, try following in terminal:-
```
./build.sh
```

Explanation of the example project
-----------

Example project contain a single html file which inlcude the `shipday-lib.js` file and call it's functionality.
The following code kind of self-explanatory:-
```
<script src="../shipday-lib/artifact/shipday-lib.js"></script>
<script type="text/javascript">
    window.onload = function () {
        const contentDiv = document.getElementById('content');
        contentDiv.innerHTML = '<h2>' + ShipdayDispatch.sayHello() + '</h2>';
    }
</script>
```