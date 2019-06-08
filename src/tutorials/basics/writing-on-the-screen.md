# Writing on the Screen

**Tutorial Info**

- Author: tpltnt
- Required Knowledge: [Anatomy of a Nannou App](./anatomy-of-a-nannou-app.md)
- Reading Time: 30 minutes

---

In this tutorial you will learn how to put text on the window of a Nannou app. This
will help you to get a (very) basic understanding of how the GUI works and how to
display information to the viewer.

First we import all the `nannou` crate to leverage its functionality. Then pull in
all the functions we need.
```rust,no_run
#![allow(unused_imports)]
extern crate nannou;  // tell rust that we want to use nannou

use nannou::prelude::*;  // get a lot of convenience functions
use nannou::ui::prelude::*;  // get a lot of functions for UI
use nannou::ui::Color::Rgba;  // we want to use color later

fn main() {}
```
We pull in `nannou::ui::Color::Rgba` explicitly because we want to use color later.
The `prelude` contains functions which are used quite often, so we do not need to
pull them all in explicitly one by one. This code code compiles, but is hardly a
Nannou app.

Here is the minimal skeleton of our app.
```rust,no_run
#![allow(unused_imports)]
#![allow(dead_code)]
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
fn event(_app: &App, model: &mut Model, event: WindowEvent) {}

// handle update event to update the (world) model accordingly
fn update(_app: &App, model: &mut Model, _update: nannou::event::Update) {}

// display the state of the world
fn view(app: &App, model: &Model, frame: Frame) -> Frame {
    frame
}
```

This has a lot going on here. The main function
```rust,no_run
# #![allow(unused_imports)]
# #![allow(dead_code)]
# extern crate nannou;  // tell rust that we want to use nannou
#
# use nannou::prelude::*;  // get a lot of convenience functions
# use nannou::ui::prelude::*;  // get a lot of functions for UI
# use nannou::ui::Color::Rgba;  // we want to use color later
#
# // model of the world (on screen)
# struct Model {
#    ui: Ui,  // data structure to handle (bare) UI
#    widget_ids: Ids,  // IDs of UI widgets
#    message: String,  // message to be displayed
#    font_id: nannou::ui::text::font::Id,  // ID of font used to display text
# }
#
# // IDs of the widgets in use
# struct Ids {
#    text: widget::Id,  // only one text widget to be used
# }
#
fn main() {
    nannou::app(model).update(update).run();  // run an app which can handle (time) updates
}

# fn model(app: &App) -> Model {
#    app.new_window().event(event).view(view).build().unwrap();  // build a window for the app, handle keystrokes, draw things on it
#    let mut ui = app.new_ui().build().unwrap(); // create a mutable UI
#    let message = String::from(
#        "PRESS 'L' TO LIST ALL FONT IDS\nON THE TERMINAL\n\nPRESS 'C' TO CHANGE FONT\nON SCREEN\n\nPRESS 'ESC' TO QUIT",
#    ); // text to display
#
#    // generate IDs for each widget
#    let widget_ids = Ids {
#        text: ui.generate_widget_id(),
#    };
#
#    // load other fonts
#    let font_map = ui.fonts_mut();  // get the mutable map of fonts for the UI
#    let font_id = font_map.ids().next().unwrap(); // use default font ID as default
#    let mut res = font_map.insert_from_file("assets/fonts/retroscape/Retroscape.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#    res = font_map.insert_from_file("assets/fonts/voynich/voynich-1.23-webfont.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#
#    // generate the actual model (of the UI)
#    Model {
#        ui,
#        message,
#        font_id,
#        widget_ids,
#    }
# }
#
# // handle events and update the (world) model accordingly
# fn event(_app: &App, model: &mut Model, event: WindowEvent) {}
#
# // handle update event to update the (world) model accordingly
# fn update(_app: &App, model: &mut Model, _update: nannou::event::Update) {}
#
# // display the state of the world
# fn view(app: &App, model: &Model, frame: Frame) -> Frame {
#    frame
# }
```
builds an app based on the model (state description) emitted by the `model()` function.
This model will be updated (by timing events) with the `update()` function. The
`main` function will finally `run()` this app until `ESC` is pressed. Exiting an app
when pressing `ESC` is the default behaviour for Nannou app. (This can be changed if you
want to, but we keep it for now.)

The model for this app looks like this:
```rust,no_run
# #![allow(unused_imports)]
# #![allow(dead_code)]
# extern crate nannou;  // tell rust that we want to use nannou
#
# use nannou::prelude::*;  // get a lot of convenience functions
# use nannou::ui::prelude::*;  // get a lot of functions for UI
# use nannou::ui::Color::Rgba;  // we want to use color later
#
# fn main() {
#    nannou::app(model).update(update).run();  // run an app which can handle (time) updates
# }
#
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
#
# fn model(app: &App) -> Model {
#    app.new_window().event(event).view(view).build().unwrap();  // build a window for the app, handle keystrokes, draw things on it
#    let mut ui = app.new_ui().build().unwrap(); // create a mutable UI
#    let message = String::from(
#        "PRESS 'L' TO LIST ALL FONT IDS\nON THE TERMINAL\n\nPRESS 'C' TO CHANGE FONT\nON SCREEN\n\nPRESS 'ESC' TO QUIT",
#    ); // text to display
#
#    // generate IDs for each widget
#    let widget_ids = Ids {
#        text: ui.generate_widget_id(),
#    };
#
#    // load other fonts
#    let font_map = ui.fonts_mut();  // get the mutable map of fonts for the UI
#    let font_id = font_map.ids().next().unwrap(); // use default font ID as default
#    let mut res = font_map.insert_from_file("assets/fonts/retroscape/Retroscape.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#    res = font_map.insert_from_file("assets/fonts/voynich/voynich-1.23-webfont.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#
#    // generate the actual model (of the UI)
#    Model {
#        ui,
#        message,
#        font_id,
#        widget_ids,
#    }
# }
#
# // handle events and update the (world) model accordingly
# fn event(_app: &App, _model: &mut Model, event: WindowEvent) {}
#
# // handle update event to update the (world) model accordingly
# fn update(_app: &App, _model: &mut Model, _update: nannou::event::Update) {}
#
# // display the state of the world
# fn view(app: &App, _model: &Model, frame: Frame) -> Frame {
#    frame
# }
```
It contains the field `ui` to hold the [user interface](https://en.wikipedia.org/wiki/User_interface)
(actually the [GUI](https://en.wikipedia.org/wiki/Graphical_user_interface)). The UI contains
[widgets](https://en.wikipedia.org/wiki/Widget_(GUI)) to make it interactive. We use the `Ids`
struct as value for for die field `widget_ids`. This struct only contains one field: `text`.
That field contains the widget ID for a text field (to display on screen). The text field is
a widget, but not interactive in the traditional sense in that you can point and click it.
Using an extra struct for the widget IDs keeps them all in one place and allows consistent
access like `model.widget_ids.text`. The message we want to display on the screen is stored
in the `message` field of the model (which holds a `String`). Text on a computer screen is
rendered using a [font](https://en.wikipedia.org/wiki/Computer_font). Usually there are many
fonts to choose from on a computer. Nannou uses an internal registry called a "font map"
to keep track of them. A font in the font map has a (numerical) ID to uniquely identify it.
This font ID is stored in the field `font_id`. Note that different versions of a typeface
(e.g. regular and bold) require different font IDs. Let's fill the model with data using
the `model()` function.

```rust,no_run
# #![allow(unused_imports)]
# #![allow(dead_code)]
# extern crate nannou;  // tell rust that we want to use nannou
#
# use nannou::prelude::*;  // get a lot of convenience functions
# use nannou::ui::prelude::*;  // get a lot of functions for UI
# use nannou::ui::Color::Rgba;  // we want to use color later
#
# fn main() {
#    nannou::app(model).update(update).run();  // run an app which can handle (time) updates
# }
#
# // model of the world (on screen)
# struct Model {
#    ui: Ui,  // data structure to handle (bare) UI
#    widget_ids: Ids,  // IDs of UI widgets
#    message: String,  // message to be displayed
#    font_id: nannou::ui::text::font::Id,  // ID of font used to display text
# }
#
# // IDs of the widgets in use
# struct Ids {
#    text: widget::Id,  // only one text widget to be used
# }
#
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
# // handle events and update the (world) model accordingly
# fn event(_app: &App, model: &mut Model, event: WindowEvent) {}
#
# // handle update event to update the (world) model accordingly
# fn update(_app: &App, model: &mut Model, _update: nannou::event::Update) {}
#
# // display the state of the world
# fn view(app: &App, model: &Model, frame: Frame) -> Frame {
#    frame
# }
```
Using `app.new_window().event(event).view(view).build().unwrap();` we build a window for the app,
assign it a function (`event`) handle window events (like keystrokes), and point to `view()` to
draw things on it. The UI is build using `app.new_ui().build().unwrap();`. In
```rust,no_run
# #![allow(unused_imports)]
# #![allow(dead_code)]
# extern crate nannou;  // tell rust that we want to use nannou
#
# use nannou::prelude::*;  // get a lot of convenience functions
# use nannou::ui::prelude::*;  // get a lot of functions for UI
# use nannou::ui::Color::Rgba;  // we want to use color later
#
# fn main() {
#    nannou::app(model).update(update).run();  // run an app which can handle (time) updates
# }
#
# // model of the world (on screen)
# struct Model {
#    ui: Ui,  // data structure to handle (bare) UI
#    widget_ids: Ids,  // IDs of UI widgets
#    message: String,  // message to be displayed
#    font_id: nannou::ui::text::font::Id,  // ID of font used to display text
# }
#
# // IDs of the widgets in use
# struct Ids {
#    text: widget::Id,  // only one text widget to be used
# }
#
# fn model(app: &App) -> Model {
#    app.new_window().event(event).view(view).build().unwrap();  // build a window for the app, handle keystrokes, draw things on it
#    let mut ui = app.new_ui().build().unwrap(); // create a mutable UI
#    let message = String::from(
#        "PRESS 'L' TO LIST ALL FONT IDS\nON THE TERMINAL\n\nPRESS 'C' TO CHANGE FONT\nON SCREEN\n\nPRESS 'ESC' TO QUIT",
#    ); // text to display
#
#    // generate IDs for each widget
    let widget_ids = Ids {
        text: ui.generate_widget_id(),
    };
#
#    // load other fonts
#    let font_map = ui.fonts_mut();  // get the mutable map of fonts for the UI
#    let font_id = font_map.ids().next().unwrap(); // use default font ID as default
#    let mut res = font_map.insert_from_file("assets/fonts/retroscape/Retroscape.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#    res = font_map.insert_from_file("assets/fonts/voynich/voynich-1.23-webfont.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#
#   // generate the actual model (of the UI)
#    Model {
#        ui,
#        message,
#        font_id,
#        widget_ids,
#    }
# }
# // handle events and update the (world) model accordingly
# fn event(_app: &App, model: &mut Model, event: WindowEvent) {}
#
# // handle update event to update the (world) model accordingly
# fn update(_app: &App, model: &mut Model, _update: nannou::event::Update) {}
#
# // display the state of the world
# fn view(app: &App, model: &Model, frame: Frame) -> Frame {
#    frame
# }
```
we make an `Ids` struct and generate a widget ID for the text field. `ui.generate_widget_id()`
is necessary to gurantee unique IDs per widget so each can interact without internal confusion.

Now that the UI widgets are taken care of the can handle the fonts to display the message text.
We learned already that Nannou tracks fonts in a (global) font map. We get access to it by
calling `ui.fonts_mut()`. We retrieve all `ids()` from it (in form of an [iterator](https://en.wikipedia.org/wiki/Iterator))
and get the `next()` ID. Since we are at the beginning this is going to be the ID of the default
font. But wait? We know that the font is a file ... where is that loaded from? You are right
in that fonts are files. They come in different formats (like [TrueType](https://en.wikipedia.org/wiki/TrueType),
or [OpenType](https://en.wikipedia.org/wiki/OpenType)). These fonts are assets for the app.
Assets are stored in the `assets/` directory. The default for is [Noto (sans regular)](https://www.google.com/get/noto/)
and it needs to be placed in the `DEFAULT_FONT_PATH`, i.e. "fonts/NotoSans/NotoSans-Regular.ttf".
We also want different fonts / typefaces. For this tutorial the fonts [retroscape](https://fontlibrary.org/en/font/retroscape)
and [voynich](https://fontlibrary.org/en/font/voynich-font) are also used. They need to be
downloaded and placed in the "assets/fonts/" directory. The font "retroscape" is loaded
into the font map using the function `insert_from_file()`. This may go wrong, so some
error checking should be done. Since the names of the variables match the names of the
fields in the model, so they can be placed there directly.

The model is set up now, let's look at the remaining logic of the app.

First we handle all (window) events.
```rust,no_run
# #![allow(unused_imports)]
# #![allow(dead_code)]
# extern crate nannou;  // tell rust that we want to use nannou
#
# use nannou::prelude::*;  // get a lot of convenience functions
# use nannou::ui::prelude::*;  // get a lot of functions for UI
# use nannou::ui::Color::Rgba;  // we want to use color later
#
# fn main() {
#    nannou::app(model).update(update).run();  // run an app which can handle (time) updates
# }
#
# // model of the world (on screen)
# struct Model {
#    ui: Ui,  // data structure to handle (bare) UI
#    widget_ids: Ids,  // IDs of UI widgets
#    message: String,  // message to be displayed
#    font_id: nannou::ui::text::font::Id,  // ID of font used to display text
# }
#
# // IDs of the widgets in use
# struct Ids {
#    text: widget::Id,  // only one text widget to be used
# }
#
# fn model(app: &App) -> Model {
#    app.new_window().event(event).view(view).build().unwrap();  // build a window for the app, handle keystrokes, draw things on it
#    let mut ui = app.new_ui().build().unwrap(); // create a mutable UI
#    let message = String::from(
#        "PRESS 'L' TO LIST ALL FONT IDS\nON THE TERMINAL\n\nPRESS 'C' TO CHANGE FONT\nON SCREEN\n\nPRESS 'ESC' TO QUIT",
#    ); // text to display
#
#    // generate IDs for each widget
    let widget_ids = Ids {
        text: ui.generate_widget_id(),
    };
#
#    // load other fonts
#    let font_map = ui.fonts_mut();  // get the mutable map of fonts for the UI
#    let font_id = font_map.ids().next().unwrap(); // use default font ID as default
#    let mut res = font_map.insert_from_file("assets/fonts/retroscape/Retroscape.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#    res = font_map.insert_from_file("assets/fonts/voynich/voynich-1.23-webfont.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#
#   // generate the actual model (of the UI)
#    Model {
#        ui,
#        message,
#        font_id,
#        widget_ids,
#    }
# }
#
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
#
# // handle update event to update the (world) model accordingly
# fn update(_app: &App, model: &mut Model, _update: nannou::event::Update) {}
#
# // display the state of the world
# fn view(app: &App, model: &Model, frame: Frame) -> Frame {
#    frame
# }
```
The `event()` function does handle window events. We use [pattern-matching](https://en.wikipedia.org/wiki/Pattern_matching)
to distinguish between the different events. Here we only care about keys being pressed
and specifically about the letters `L` (or `l`) and `C` (or `c`). The `Key`-event does
not distinguish between upper- and lowercase characters. If `L` is pressed we go over
all `ids()` in the font map and print them on the terminal. If `C` is pressed we create
a vector of font IDs to cycle through. Then we go over the all IDs in the global font map and insert
them in the front of the vector we just created. Going over the global font map yields
the IDs from high to low, which is why we build the vector back to front (rather than
the more intuitive other way around). The we go over this list and check if the
current font ID is the same as the one we use for displaying the text. If so we use
the next ID as the (new) font ID for displaying text and set the `changed` flag
to indicate that we modified the font ID. If the `change` flag is not set, that
means the current font ID is last in the list and thus we choose the first ID
from that list as the next one. This results in a continous cycling through the
font IDs whenever `C` is pressed.

Now that the font ID is chosen we create a new text widget (and thus refresh its
content) on every update event. By default these update events occur at a rate
of 60 frames per second.
```rust,no_run
# #![allow(unused_imports)]
# #![allow(dead_code)]
# extern crate nannou;  // tell rust that we want to use nannou
#
# use nannou::prelude::*;  // get a lot of convenience functions
# use nannou::ui::prelude::*;  // get a lot of functions for UI
# use nannou::ui::Color::Rgba;  // we want to use color later
#
# fn main() {
#    nannou::app(model).update(update).run();  // run an app which can handle (time) updates
# }
#
# // model of the world (on screen)
# struct Model {
#    ui: Ui,  // data structure to handle (bare) UI
#    widget_ids: Ids,  // IDs of UI widgets
#    message: String,  // message to be displayed
#    font_id: nannou::ui::text::font::Id,  // ID of font used to display text
# }
#
# // IDs of the widgets in use
# struct Ids {
#    text: widget::Id,  // only one text widget to be used
# }
#
# fn model(app: &App) -> Model {
#    app.new_window().event(event).view(view).build().unwrap();  // build a window for the app, handle keystrokes, draw things on it
#    let mut ui = app.new_ui().build().unwrap(); // create a mutable UI
#    let message = String::from(
#        "PRESS 'L' TO LIST ALL FONT IDS\nON THE TERMINAL\n\nPRESS 'C' TO CHANGE FONT\nON SCREEN\n\nPRESS 'ESC' TO QUIT",
#    ); // text to display
#
#    // generate IDs for each widget
#    let widget_ids = Ids {
#        text: ui.generate_widget_id(),
#    };
#
#    // load other fonts
#    let font_map = ui.fonts_mut();  // get the mutable map of fonts for the UI
#    let font_id = font_map.ids().next().unwrap(); // use default font ID as default
#    let mut res = font_map.insert_from_file("assets/fonts/retroscape/Retroscape.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#    res = font_map.insert_from_file("assets/fonts/voynich/voynich-1.23-webfont.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#
#    // generate the actual model (of the UI)
#    Model {
#        ui,
#        message,
#        font_id,
#        widget_ids,
#    }
# }
#
# // handle events and update the (world) model accordingly
# fn event(_app: &App, model: &mut Model, event: WindowEvent) {}
#
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
#
# fn view(app: &App, model: &Model, frame: Frame) -> Frame {
#    let draw = app.draw(); // get drawing context /frame
#    draw.background().rgb(0.0, 0.0, 0.0); // set background to black -> clear screen
#    draw.to_frame(app, &frame).unwrap(); // write the result of drawing to window's OpenGL frame
#    model.ui.draw_to_frame(app, &frame).unwrap(); // draw UI on frame
#    frame // return new frame
# }

```
First we instantiate the UI widgets so they can be drawn later. We create a
text(box) widget with the message (string) we stored in the (global) app
model. We then set the layout of the box and finally adjust the color
of the text using `Rgba`. Finally we set the ID of this textbox
to the one by the model for tracking UI elements and tie it to
the (current) instance of the UI. Now it can be displayed anytime
the UI is called upon.

Finally state of the model is displayed via
```rust,no_run
# #![allow(unused_imports)]
# #![allow(dead_code)]
# extern crate nannou;  // tell rust that we want to use nannou
#
# use nannou::prelude::*;  // get a lot of convenience functions
# use nannou::ui::prelude::*;  // get a lot of functions for UI
# use nannou::ui::Color::Rgba;  // we want to use color later
#
# fn main() {
#    nannou::app(model).update(update).run();  // run an app which can handle (time) updates
# }
#
# // model of the world (on screen)
# struct Model {
#    ui: Ui,  // data structure to handle (bare) UI
#    widget_ids: Ids,  // IDs of UI widgets
#    message: String,  // message to be displayed
#    font_id: nannou::ui::text::font::Id,  // ID of font used to display text
# }
#
# // IDs of the widgets in use
# struct Ids {
#    text: widget::Id,  // only one text widget to be used
# }
#
# fn model(app: &App) -> Model {
#    app.new_window().event(event).view(view).build().unwrap();  // build a window for the app, handle keystrokes, draw things on it
#    let mut ui = app.new_ui().build().unwrap(); // create a mutable UI
#    let message = String::from(
#        "PRESS 'L' TO LIST ALL FONT IDS\nON THE TERMINAL\n\nPRESS 'C' TO CHANGE FONT\nON SCREEN\n\nPRESS 'ESC' TO QUIT",
#    ); // text to display
#
#    // generate IDs for each widget
#    let widget_ids = Ids {
#        text: ui.generate_widget_id(),
#    };
#
#    // load other fonts
#    let font_map = ui.fonts_mut();  // get the mutable map of fonts for the UI
#    let font_id = font_map.ids().next().unwrap(); // use default font ID as default
#    let mut res = font_map.insert_from_file("assets/fonts/retroscape/Retroscape.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#    res = font_map.insert_from_file("assets/fonts/voynich/voynich-1.23-webfont.ttf");
#    if let Err(e) = res {
#        println!("{}", e);
#        panic!("loading font failed");
#    }
#
#    // generate the actual model (of the UI)
#    Model {
#        ui,
#        message,
#        font_id,
#        widget_ids,
#    }
# }
#
# // handle events and update the (world) model accordingly
# fn event(_app: &App, model: &mut Model, event: WindowEvent) {}
#
# // handle update event to update the (world) model accordingly
# fn update(_app: &App, model: &mut Model, _update: nannou::event::Update) {}
#
fn view(app: &App, model: &Model, frame: Frame) -> Frame {
    let draw = app.draw(); // get drawing context /frame
    draw.background().rgb(0.0, 0.0, 0.0); // set background to black -> clear screen
    draw.to_frame(app, &frame).unwrap(); // write the result of drawing to window's OpenGL frame
    model.ui.draw_to_frame(app, &frame).unwrap(); // draw UI on frame
    frame // return new frame
}
```
We draw a black background on a frame. This frame is displayed so that
it looks like we cleared the screen. On top of that we draw the
UI which we are tracking with the global model. After all this we
return the `frame` to be shown by the Nannou app.

These were a lot of things to learn and do to put some text on the
screen. Now go ahead and change the `message` or how the app
reacts to different keys being pressed, or where and how text is
drawn.
