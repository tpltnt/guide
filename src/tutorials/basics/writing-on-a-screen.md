# Anatomy of a Nannou App

**Tutorial Info**

- Author: tpltnt
- Required Knowledge: [Anatomy of a Nannou App](./anatomy-of-a-nannou-app.md)
- Reading Time: 10 minutes

---


```rust,no_run
//! Draw text on screen.
//!
//! Fonts are assets for an nannou app and should be located in
//! the "assets" directory. The default font can be set by editing
//! the DEFAULT_FONT_PATH contant in src/ui.rst. The font file
//! referenced by DEFAULT_FONT_PATH is loaded when the app is
//! started.
extern crate nannou;  // tell rust that we want to use nannou

use nannou::prelude::*;  // get a lot of convenience functions
use nannou::ui::prelude::*;  // get a lot of functions for UI
use nannou::ui::Color::Rgba;  // we want to use color later

fn main() {
    nannou::app(model).update(update).run();  // run an app which can handle (time) updates
}

// model of the world (on screen)
struct Model {
    ui: Ui,  // data structure to handle (bare) UI
    widget_ids: Ids,  // IDs of UI widgets
    message: String,  // message to be displayed
    font_id: nannou::ui::text::font::Id,  // ID of font used to display text
}

// IDs of the widgets in use
struct Ids {
    text: widget::Id,  // only one text widget to be used
}

// Generate a model of the world (to work with)
fn model(app: &App) -> Model {
    app.new_window().event(event).view(view).build().unwrap();  // build a window for the app, handle keystrokes, draw things on it
    let mut ui = app.new_ui().build().unwrap(); // create a mutable UI
    let message = String::from(
        "PRESS 'L' TO LIST ALL FONT IDS\nON THE TERMINAL\n\nPRESS 'C' TO CHANGE FONT\nON SCREEN\n\nPRESS 'ESC' TO QUIT",
    ); // text to display

    // generate IDs for each widget
    let widget_ids = Ids {
        text: ui.generate_widget_id(),
    };

    // load other fonts
    let font_map = ui.fonts_mut();  // get the mutable map of fonts for the UI
    let font_id = font_map.ids().next().unwrap(); // use default font ID as default
    let mut res = font_map.insert_from_file("assets/fonts/retroscape/Retroscape.ttf");
    if let Err(e) = res {
        println!("{}", e);
        panic!("loading font failed");
    }
    res = font_map.insert_from_file("assets/fonts/voynich/voynich-1.23-webfont.ttf");
    if let Err(e) = res {
        println!("{}", e);
        panic!("loading font failed");
    }

    // generate the actual model (of the UI)
    Model {
        ui,
        message,
        font_id,
        widget_ids,
    }
}

// handle events and update the (world) model accordingly
fn event(_app: &App, model: &mut Model, event: WindowEvent) {
    match event {
        // catch and handle key(board) presses
        KeyPressed(key) => {
            // list all font IDs found
            if Key::L == key {
                // get the font map
                let fontmap = model.ui.fonts_mut();

                // list all IDs for the fonts in the map
                println!("font IDs found:");
                for id in fontmap.ids() {
                    println!("\t{:?}", id);
                }
            }

            // change display font
            if Key::C == key {
                // the higher IDs come first, thus the default last
                let mut font_ids = Vec::new();
                for id in model.ui.fonts_mut().ids() {
                    font_ids.insert(0, id)
                }

                // font IDs are sorted from low to high
                let mut use_this = false;
                let mut changed = false;
                for id in &font_ids {
                    if use_this {
                        model.font_id = *id;
                        changed = true;
                        break; // no need to iterate further
                    }
                    if model.font_id == *id {
                        // we should use the next font
                        use_this = true;
                    }
                }

                // check if new ID was (not) set
                if !changed {
                    // no new ID set -> current ID is last in list -> use first element (again)
                    model.font_id = font_ids[0]; // use default font ID as default
                }
            }
        }
        // ignore all other events
        _ => (),
    }
}

// handle update event to update the (world) model accordingly
fn update(_app: &App, model: &mut Model, _update: nannou::event::Update) {
    // enable instantiation of widgets (to be drawn later)
    let ui = &mut model.ui.set_widgets();

    // create a textbox widget with text from model
    let textbox = widget::Text::new(&model.message)
        .center_justify() // centered text
        .font_id(model.font_id) // set currently selected font by ID
        .font_size(34) // font size 34
        .x(-50.0) // slightly left of vertical center
        .y(0.0) // horizontal center
        .color(Rgba(1.0, 0.0, 0.0, 1.0)); // pure red font/text color
    textbox.set(model.widget_ids.text, ui); // set (widget) ID for text box
}

// display the state of the world
fn view(app: &App, model: &Model, frame: Frame) -> Frame {
    let draw = app.draw(); // get drawing context /frame
    draw.background().rgb(0.0, 0.0, 0.0); // set background to black -> clear screen
    draw.to_frame(app, &frame).unwrap(); // write the result of drawing to window's OpenGL frame
    model.ui.draw_to_frame(app, &frame).unwrap(); // draw UI on frame
    frame // return new frame
}
```
