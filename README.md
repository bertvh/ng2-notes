# ng2-notes

Some notes on the stuff I struggeled with learning Angular 2 (beta0)

## If This, Try That

### Exception `Unexpected directive value 'undefined' on the View of component 'MyComponent'`

- If your component uses a subcomponent, declared in a single file like the file below, **declare MySubcomponent first**.

``` typescript
@Component
@View { directives: [MySubComponent]}
export class MyComponent {}

@Component
@View
export class MySubComponent {} // This should go first
```

## Adding content inside a component/directive (transclusion)

The `ng-content` directive allows you to insert content inside a (custom) component very easily. This is similar to but much more powerful than Angular 1 translusion.

#### A single content block

Using 'my-component'
``` html
<my-component>
  Some content
</my-comonent>
```

The 'my-component' view template:
``` html
<div> Something something </div>
<ng-content></ng-content> <!-- This is where 'Some content' will appear -->
<div> The end </div>
```
#### Multiple content blocks

Use the `select` property on `ng-content` to determine which part of the provided content should go where.

Using 'my-component'
``` html
<my-component>
  <div class="first-paragraph">Angular 2 is cool...</div>
  <div class="second-paragraph">...but not always documented.</div>
</my-comonent>
```

The 'my-component' view template:
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

## Getting a reference to a component from another component

Angular 2 by default has only one-way databinding. The flow from controller to view is by data-flow, view to controller by events. Now what if you want to invoke a method on a subcomponent? How can you get a reference to a subcomponent? One way is to let the subcomponent fire an event when it is constructed, passing itself as a reference.

The subcomponent - a modal dialog
``` typescript
@Component({...})
@View({...})
export class Modal implements OnInit {
  /**
   * Fires an event when the modal is ready with a pointer to the modal.
   */
  @Output('loaded') loadedEmitter: EventEmitter<Modal> = new EventEmitter<Modal>();
  
  onInit() {
    this.loadedEmitter.next(this);
  }
}
```

The view template of App.ts
``` html
<modal (loaded)="modalLoaded($event)"></modal>
<div>Other content of the App view</div>
```

App.ts, getting a reference to the modal
``` typescript

@Component({...})
@View({...})
export class App {
modalLoaded(modal: Modal) {
    this.modal = modal;
  }
}
```

## Getting the (view) children of a component

Suppose you're making a wizard component with child pages and you need to refer the children from the parent component.
``` xml
<wizard>
  <wizard-page [label]="page1"></wizard-page>
  <wizard-page [label]="page2"></wizard-page>
</wizard>
```

The wizard needs to be able to do something like:
```java
this.activePage = wizardPages[0];
wizardPages[0].isValid();
[...]
```

``` java
@Component({
  selector: 'wizard'
})
@View({
  directives: [WizardPage]
})
export class Wizard implements AfterViewInit {

  // This is the key: viewChildrew will contain all the wizard pages.
  @ViewChildren(WizardPage) viewChildren: QueryList<WizardPage>;

  // Viewchildren (the WizardPages) are available now.
  ngAfterViewInit() {
    // viewChild is updated after the view has been initialized
    for(let child of this.viewChildren.toArray()) {
      console.log(child);
    }
  }
}

@Component({
  selector: 'wizard-page'
})
export class WizardPage implements OnInit {
  [...]
}
```

