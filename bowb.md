# Blasting off with Bootstrap (by Code School)

## Important classes
* `container` - responsively add margins, center and wrap content
* `container-fluid` - allows for stretching if it is desired on larger screens
* `row` - let you specify rows in your layout
* `col-md-offset-*` - leave specific number of columns empty (on the left)
* `hidden-*` - hid element in specific size
* `visible-*` - show element in specific size
* `lead` - make a text stand out from the rest of the page
* `text-*` - aligment classes for text
* `list-unstyled` - remove bullets from list
* `list-inline` - display list horizontaly
* `well` - enclose elements in grey background and border with rounded corners
* `well-*` - add more padding to `well` element
* `btn` - bootstrap button class
* `btn-[xs|sm|lg]` - define size of button
* `but-[default|primary|success|info|danger|warning]` - colors for buttons
* `navbar-brand` - float the text to the left, increase font size etc

## Grid layout

Grid layout uses 12 columns.

### Table of columns sizes

Grid Size | Grid Class | Offset Class | Screen Size
--- | --- | --- | ---
*Extra small* | col-xs-* | col-xs-offset-* | 0px+
*Small* | col-sm-* | col-sm-offset-* | 768px+
*Medium* | col-md-* | col-md-offset-* | 992px+
*Large* | col-lg-* | col-lg-offset-* | 1200px+

## Glyphicons

Example:

```html
<i class='glyphicon glyphicon-briefcase'></i>
```

Glyphicons look can be styled in the same way as text.