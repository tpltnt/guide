# Porting from other frameworks

Nannou is inspired by other creative coding frameworks inspired like [Processing](https://processing.org),
[openFrameworks](https://openframeworks.cc), and [Cinder](https://libcinder.org/).
This section is intended to help everyone making the switch to nannou and bringing their code with them.

**note: This is very much a work in progress. It will be extended as we learn about porting issues.**

## General
**This section shall contain general information**

## Processing
Processing has two famous functions. The `setup()` function prepares everything and takes care of
the initial setup. The `draw()` actuallly changes things on the screen.

Sometimes the `setup()` function is used to prepare the screen. Sometimes code already draws
in this phase (e.g. to set the background). This behaviour can be emulated using `.nth()`, which
counts the number of frames displayed in the associated window.
```rust
fn view(app: &App, m: &Model, frame: &Frame) {
    let draw = app.draw();
    if frame.nth() == 0 {
        // do one-time preparations of the screen here
    }
    // general drawing code
    draw.to_frame(app, &frame).unwrap();
}
```


## openFrameworks
**This section shall contain openFrameworks-specific information**

## Cinder
**This section shall contain Cinder-specific information**
