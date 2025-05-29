
**How to Customize Your Color Set:**

You can easily change or expand the set of colors used.

1.  **Modify the `$colors` Slice:** Just change the strings inside the `slice` function at the top of the shortcode. Make sure you use valid Bootstrap color names *that also have corresponding CSS rules defined* in the `<style>` block. The current CSS supports `warning`, `success`, `danger`, and `info`.

    *   **Example with 5 Colors (Adding a custom one - needs CSS):**
        If you wanted to add, say, a purple color, you would first need to:
        a.  Define the CSS for it (mimicking the existing color rules):

            ```css
            /* --- ADD this in the <style> block --- */
            .widget-49 .widget-49-title-wrapper .widget-49-date-purple {
              background-color: var(--bs-purple-bg-subtle, #e7dff6); /* Example purple */
            }
            .widget-49 .widget-49-title-wrapper .widget-49-date-purple .widget-49-date-day,
            .widget-49 .widget-49-title-wrapper .widget-49-date-purple .widget-49-date-month {
              color: var(--bs-purple-text-emphasis, #5e2f91); /* Example purple text */
            }

            /* And for the button */
            .btn-flash-border-purple {
              color: var(--bs-purple, #6f42c1); /* Example purple */
              border-color: var(--bs-purple, #6f42c1);
            }
            .btn-flash-border-purple:hover {
              color: #fff;
              background-color: var(--bs-purple, #6f42c1);
              border-color: var(--bs-purple, #6f42c1);
            }
            /* --- END of added CSS --- */
            ```

        b.  Then update the slice:

            ```go
            {{ $colors := slice "warning" "success" "danger" "info" "purple" }}
            ```

    *   **Example using only 3 specific colors:**

        ```go
        {{ $colors := slice "success" "danger" "info" }}
        ```

2.  **Keep the Random Logic:** The line `{{ $randomColor := index (shuffle $colors) 0 }}` will automatically work with your modified `$colors` slice, picking randomly from the new set you defined.

**Alternative: Using Colors in Rotation**

If you prefer a guaranteed rotation instead of pure randomness (e.g., post 1 is warning, post 2 is success, post 3 is danger, post 4 wraps back to warning), you can use the modulo operator (`mod`) with the loop index.

Replace this line:

```go
{{ $randomColor := index (shuffle $colors) 0 }}
```

With these lines:

```go
{{ $numColors := len $colors }}
{{ $colorIndex := mod $index $numColors }} {{/* Get index 0, 1, 2, ... wrapping around */}}
{{ $rotatingColor := index $colors $colorIndex }} {{/* Pick color based on calculated index */}}
```

And then make sure you use `$rotatingColor` where `$randomColor` was used:

```html
<div class="widget-49-date widget-49-date-{{ $rotatingColor }}">
  ...
</div>
...
<a href="{{ .RelPermalink }}" class="btn-flash-border btn-flash-border-{{ $rotatingColor }}">Read More</a>
```

**Summary:**

*   **Yes, it's currently random.**
*   **To change the colors:** Modify the `{{ $colors := slice "..." }}` line at the top. Ensure the color names you use have corresponding CSS rules defined in the `<style>` block.
*   **For rotation:** Replace the `shuffle` logic with the `mod $index` logic shown above.

Choose whichever method (random or rotation) and color set you prefer!
