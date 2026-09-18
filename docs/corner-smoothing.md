# Continuous surface corners

Use `.rounded(px(26.0)).corner_smoothing(0.6)` on a styled surface. Zero keeps the existing circular corners; one uses a fourth-power superellipse. Values above one strengthen the squircle (exponent `2 + 2 * smoothing`); inputs are clamped to `[0, 2]` and non-finite values become zero. Radius fitting continues to use the existing corner-radius clamp.

The fill, border, drop shadows and inset shadows share this treatment. Custom elements can opt in with:

- `Window::paint_quad_with_corner_smoothing`
- `Window::paint_drop_shadows_with_corner_smoothing`
- `Window::paint_inset_shadows_with_corner_smoothing`
- `Window::paint_backdrop_blur_with_corner_smoothing`

A backdrop surface must pass the same radii and smoothing factor as its foreground chrome. Existing paint methods and `PaintQuad` struct literals remain compatible and default to zero smoothing. This API does not clip descendants or change image corner masks.

Metal, WGSL and HLSL implement the same gradient-normalized implicit curve. The unsmoothed path retains the original distance and shadow functions. The additional fields in scene primitives are mirrored in shader layouts; Metal generates its structs from Rust, and WGSL tests check host offsets and stride.

The implementation was informed by the renderer coverage of [gpui-ce #235](https://github.com/gpui-ce/gpui-ce/pull/235). It uses a superellipse rather than that PR's Figma-flavored Bézier construction and does not claim identical curves or its smoothing-preservation behavior.
