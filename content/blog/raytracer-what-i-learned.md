---
title: "What I learned from writing a raytracer in Rust"
date: 2023-07-22T00:00:00-05:00
tags: ["rust", "beginner"]
summary: "Sometimes you learn the most by doing."
cover:
    image: "images/undraw_lightbulb_moment_re_ulyo.png"
    alt: "drawing of a man lighting a light bulb"
---

Sometimes you learn the most by doing. That's what I realized yet again when writing [a toy raytracer in Rust](https://github.com/joshaustintech/raytracer) during what little free time I had during paternity leave. I had religiously watched all of the [Rust videos by No Boilerplate](https://www.youtube.com/@NoBoilerplate) and needed to stop watching and start writing. I came across the Peter Shirley's [Raytracer In A Weekend](https://raytracing.github.io) tutorial, written in C++, and decided to try following along and writing it in Rust.

Last I left off, I was able to create a reflective material, which was fun.

![a raytraced render of one sphere reflecting another](/images/render_1688285882.jpg)

Writing a toy raytracer in Rust helped me to fill some of the gaps in my learning so far, including:
- Traits
- Tuples
- Modules
- Operator Overloading
- Type Aliasing
- File I/O

Something that surprised me (but shouldn't have) was that the C/C++ style of calling a method on mutable objects was noticeably less efficient than *returning* them. Kudos to [Warren Henning](https://mastodon.social/@whenning) for pointing this out when I was frustrated at the amount of time going into rendering a relatively small and simple image.

For example, any implementation of this C++ header:
```cpp
class material {
    public:
        virtual bool scatter(
            const ray& r_in,       // input ray
            const hit_record& rec, // input hit record
            color& attenuation,    // output color if return value is true, otherwise nullref
            ray& scattered         // output ray if return value is true, otherwise nullref
        ) const = 0;
};
```

Was noticeably faster after simplified in Rust to:
```rust
pub trait Material {
    fn scatter(
        &self,
        ray: &Ray,
        hit_record: &HitRecord
    ) -> Option<(Color, Ray)>;
}
```

Whenever I get the time, I would love to rewrite the renderer from the beginning with multithreading, better command-line input/output, and overall cleaner code in mind. Beyond that, I'd love to expand it to have a scene file format, simple animations, a GUI, GPU support, and more.

For now, I want to finish off the [Rustlings](https://github.com/rust-lang/rustlings) coding exercise and attempt to widen my range of Rust projects to include full-stack web development, artificial intelligence, and embedded development.