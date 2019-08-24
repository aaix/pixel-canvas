# Pixel Canvas

This crate is designed to make it easy to build interactive computer art with
just a pixel buffer. For inspiration, consider looking at
<https://www.shadertoy.com> and <http://www.iquilezles.org/www/index.htm>,
there are a lot of cool art and techniques on display there!

## Usage

To make a piece of art, you create and configure a `Canvas` object, and then
you ask it to `render` you. The canvas will do state management and hand you an
image to modify. Whatever modifications you make to the image will be displayed
on the screen.

```rust
use pixel_canvas::{Canvas, Color, MouseState};

fn main() {
    // Configure the window that you want to draw in. You can add an event
    // handler to build interactive art. Input handlers for common use are
    // provided.
    let canvas = Canvas::new(512, 512)
        .title("Tile")
        .state(MouseState::physical())
        .input(MouseState::handle);
    // The canvas will render for you at up to 60fps.
    canvas.render(|mouse, image| {
        // Modify the `image` based on your state.
        let width = image.width() as usize;
        for (y, row) in image.chunks_mut(width).enumerate() {
            for (x, pixel) in row.iter_mut().enumerate() {
                let dx = x as i32 - mouse.x;
                let dy = y as i32 - mouse.y;
                let dist = dx * dx + dy * dy;
                *pixel = Color {
                    r: if dist < 128 * 128 { dy as u8 } else { 0 },
                    g: if dist < 128 * 128 { dx as u8 } else { 0 },
                    b: (x * y) as u8,
                }
            }
        }
    });
}
```