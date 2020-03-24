# Updating old code

Sometimes you have to touch old code and make it work in a new environment.
This sections helps you deal with problems you might encounter.

In general it helps to read all the CHANGELOG files from your old source
version to the target you want to upgrade to.


## Sketches don't run

Your old code looks like this:
```
extern crate nannou;
use nannou::prelude::*;

fn main() {
    nannou::sketch(view);
}

fn view(_app: &App, _frame: Frame) {
    // the sketch code
}
```

Nannou 0.13 introduced a change to allow the user to specify the size of the sketch in this way
```
nannou::sketch(view).size(width, height).run()
```

This required changing the `sketch()` function to return a builder type rather than running immediately as done in the past.
Specifying the size of a window in the view function felt awkward and caused a panic on Windows after a winit update.

Adding `.run()` after the sketch function should display the sketch again.
```
extern crate nannou;
use nannou::prelude::*;

fn main() {
    nannou::sketch(view).run();
}

fn view(_app: &App, _frame: Frame) {
    // the sketch code
}
```

## Window sizing does not work / with_dimension is gone

If you have code like
```
new_window().with_dimension(600,600)
```
won't work after 0.13.1

Use `size()` in this manner:
```
new_window().size(600, 600)
```
