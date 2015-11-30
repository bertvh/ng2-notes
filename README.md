# ng2-notes

Some notes on the stuff I struggeled with learning Angular 2 (0.46)

## Adding content inside a component/directive (transclusion)

The `ng-content` directive allows you to insert content insude a (custom) component very easily.

#### A single content block
``` html
<my-component>
  Some content
</my-comonent>
```

The component template:
``` html
<div> Something something </div>
<ng-content></ng-content> <!-- This is where 'Some content' will appear -->
<div> The end </div>
```
#### Multiple content blocks

Use the `select` property on `ng-content` to determine which part of the provided content should go where.

``` html
<my-component>
  <div class="first-paragraph">Angular 2 is cool...</div>
  <div class="second-paragraph">...but not always documented.</div>
</my-comonent>
```

The component template:
``` html
<div> Something something </div>
  <p>
    <ng-content select=".first-paragraph"></ng-content> <!-- This is where 'Angular 2 is cool...' will appear -->
  </p>
  <p>
    <ng-content select=".second-paragraph"></ng-content> <!-- ...but not always documented.' will appear -->
  </p>
<div> The end </div>
```
