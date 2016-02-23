# ![Angular 2 Style Guide](https://raw.githubusercontent.com/mgechev/angular2-style-guide/master/assets/logo.png) Angular 2 Style Guide

[![Join the chat at https://gitter.im/mgechev/angular2-style-guide](https://badges.gitter.im/mgechev/angular2-style-guide.svg)](https://gitter.im/mgechev/angular2-style-guide?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

The purpose of the following style guide is to present a set of best practices and style guidelines for the development of Angular 2 applications.
If you are looking for an opinionated style guide for syntax, conventions, and structuring Angular 2 applications, then you can step in!

**Disclaimer: The document is in alpha version which means that some the guidelines will change and new will be added.**

You are welcome to join the discussion of the best [practices here](https://github.com/mgechev/angular2-style-guide/issues).

The guidelines described below are based on:

1. Angular 2 [source code](https://github.com/angular/angular).
2. Suggestions by [Miško Hevery](https://github.com/mhevery) from his technical review of "[Switching to Angular 2](https://www.packtpub.com/web-development/switching-angular-2)".
3. My own development experience working in a team on large-scale Angular 2 application.
4. [John Papa's AngularJS 1.x style guide](https://github.com/johnpapa/angular-styleguide), for being consistent with the directory structure and testing across the different major versions of the framework.

## Table of Contents

1. [Directory Structure](#directory-structure)
2. [Directives and Components](#directives-and-components)
3. [Services and Dependency Injection](#services-and-dependency-injection)
4. [Pipes](#pipes)
5. [Routing](#routing)
6. [Forms](#forms)
7. [Reusable libraries](#reusable-libraries)
8. [Testing](#testing)
9. [Change Detection](#change-detection)
10. [TypeScript specific practices](#typescript-specific-practices)
11. [ES2015 and ES2016 specific practices](#es2015-and-es2016-specific-practices)
12. [ES5 specific practices](#es5-specific-practices)
13. [License](#license)

## Directory Structure

* Group files by the [bounded context](http://martinfowler.com/bliki/BoundedContext.html) they belong to. When a context (directory, for insance) grows to contain more than 15 files, start to consider creating a separate context by-type for them. Your threshold may be different, so adjust as needed.

  ```
  .
  ├── admin
  │   ├── home-dashboard.component.ts
  │   ├── home-dashboard.component.html
  │   ├── home-dashboard.component.css
  │   ├── home-dashboard.component.spec.ts
  │   ├── login.component.ts
  │   ├── login.component.spec.ts
  │   ├── admin.ts
  │   ├── user-management.service.ts
  │   └── order-management.service.ts
  ├── shared
  │   ├── components
  │   │   ├── avatar.component.ts
  │   │   ├── avatar.component.html
  │   │   ├── login-form.component.ts
  │   │   ├── login-form.component.html
  │   │   ├── login-form.component.css
  │   │   └── login-form.component.spec.ts
  │   ├── directives
  │   │   ├── form-validator.directive.ts
  │   │   ├── form-validator.directive.spec.ts
  │   │   ├── tooltip.directive.ts
  │   │   └── tooltip.directive.spec.ts
  │   └── services
  │       └── authorization.service.ts
  ├── pipes
  │   ├── format-order-name.pipe.ts
  │   └── format-order-name.pipe.spec.ts
  └── shop
      ├── components
      │   ├── edit-profile.component.ts
      │   ├── edit-profile.component.html
      │   ├── edit-profile.component.css
      │   ├── edit-profile.component.spec.ts
      │   ├── home.component.ts
      │   ├── home.component.spec.ts
      │   ├── home.component.html
      │   ├── register.component.ts
      │   └── register.component.spec.ts
      ├── models
      │   ├── shopping-cart.ts
      │   ├── shopping-item.ts
      │   └── user.ts
      └── services
          └── checkout.service.ts
  ```

  *Why?*: The level of reusability of logic between bounded contexts should be low. On the other hand each code unit will belong to the bounded context it is associated with and will not pollute the directory structure.

  *Why?*: Separation by bounded context will allow easier code reusability. In the general case a small, to medium applicaiton will contain a single bounded context, which will lead to a flat directory structure as the following:

  ```
  shop
  ├── components
  │   ├── edit-profile.component.ts
  │   ├── edit-profile.component.spec.ts
  │   ├── edit-profile.html
  │   ├── home.component.ts
  │   ├── home.component.spec.ts
  │   ├── home.html
  │   ├── register.component.ts
  │   └── register.component.spec.ts
  ├── models
  │   ├── shopping-cart.ts
  │   ├── shopping-item.ts
  │   └── user.ts
  └── services
      └── checkout.service.ts
  ```

  *Why?*: A developer can locate the code, identify what each file represents at a glance, the structure is flat as can be, and there is no repetitive nor redundant names.

  *Why?*: When there are a lot of files (15+) locating them is easier with a consistent folder structures and more difficult in flat structures.

* Define only a single code unit (component, directive, service, pipe, etc.) per file. If the unit uses other internal for the given module logic you can keep it in the same module, without exporting it.

  *Why?*: The definitions will be easier to find simply by looking at the directory structure.

  *Why?*: Do not export private APIs because you need to manage them and keep them consistent for the end users.

* Name the files which contain component, directives, pipes and services definition with the corresponding suffix.

  ```
  // Contains the "home-dashboard" component
  home-dashboard.component.ts

  // Contains the ShoppingCart model. Notice that there is no suffix for the models.
  shopping-cart.ts

  // Contains the logic for the "checkout" service.
  checkout.service.ts
  ```

* Keep the modules self-contained and coherent. Each module should have a [single reason to change](https://en.wikipedia.org/wiki/Single_responsibility_principle).

  *Why?*: This way the modules will be more reusable, and thus composable.

* Name the modules/directories with `lower-kebab-case`.

  *Why?*: Using lower case will not cause problem across platforms with different case sensitivity.

  *Why?*: By using only English letters, numbers and dash (`-`) the file names will be consistent across platforms.

  *Why?*: Keeps consistency with AngularJS 1.x style guidelines.

  ```
  /* CORRECT */
  tooltip.directive.ts
  user.service.ts
  ```
  **[Table of Contents](#table-of-contents)**

## Directives and Components

* Use attributes as selectors for your directives.

  *Why?*: There could be many directives per element, which makes it more suitable to use attributes as opposed to elements.

* Use `lowerCamelCase` for naming the selectors of your directives.

  *Why?*: Keeps the names of the properties defined in the controllers that are bound to the view consistent with the attribute names.

  *Why?*: The Angular 2 HTML parser is case sensitive so `lowerCamelCase` attributes are well supported.

* Use custom prefix for the selector of your directives (for instance below is used the prefix `sg` from **S**tyle **G**uide).

  *Why?*: This way you will be able to prevent name collisions.

  ```ts
  /* WRONG */
  @Directive({
    selector: '[tooltip]'
  })
  class BootstrapTooltipDirective {}

  @Directive({
    selector: '[tooltip]'
  })
  class CustomTooltipDirective {}

  @Component({
    selector: 'app',
    template: `...`,
    directives: [CustomTooltip, BootstrapTooltip]
  })
  class AppComponent {}
  ```

  ```ts
  /* CORRECT */
  @Directive({
    selector: '[bsTooltip]'
  })
  class BootstrapTooltipDirective {}

  @Directive({
    selector: '[myTooltip]'
  })
  class CustomTooltipDirective {}

  @Component({
    selector: 'sg-app',
    template: `...`,
    directives: [CustomTooltip, BootstrapTooltip]
  })
  class AppComponent {}
  ```

* Name directives' controllers with `Directive` suffix and components' controllers with `Component` suffix. The name of any directive or component should be formed following the rule `BasicDescription` + `Directive` or `Component`.

  ```ts
  /* CORRECT */
  @Directive({
    selector: '[sgTooltip]`
  })
  class TooltipDirective {}

  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent {}
  ```

  *Why?*: When any code unit is imported the consumer will know how to use it based on its name, i.e. `ButtonComponent` means that:

    - This is a controller of a component.
    - The component should be used as an element.

* Use [`@HostListener`](https://angular.io/docs/ts/latest/api/core/HostListener-var.html) and [`@HostBinding`](https://angular.io/docs/ts/latest/api/core/HostBinding-var.html) instead of the [`host`](https://angular.io/docs/ts/latest/api/core/Host-var.html) property of the [`@Directive`](https://angular.io/docs/ts/latest/api/core/Directive-decorator.html) and [`@Component`](https://angular.io/docs/ts/latest/api/core/Component-decorator.html) decorators:

  *Why?*: The name of the property, or method name associated to [`@HostBinding`](https://angular.io/docs/ts/latest/api/core/HostBinding-var.html) or respectively [`@HostListener`](https://angular.io/docs/ts/latest/api/core/HostListener-var.html) should be modified only in a single place - in the directive's controller. In contrast if you use [`host`](https://angular.io/docs/ts/latest/api/core/Host-var.html) you need to modify both the property declaration inside the controller, and the metadata associated to the directive.

  *Why?*: The metadata declaration attached to the directive is shorter and thus more readable.

  ```ts
  /* WRONG */
  @Directive({
    selector: '[sgSample'],
    host: {
      '(mouseenter)': 'onMouseEnter()',
      'role': 'button'
    }
  })
  class SampleDirective {
    button;
    onMouseEnter() {...}
  }
  ```

  ```ts
  /* CORRECT */
  @Directive({
    selector: '[sgSample]'
  })
  class SampleDirective {
    @HostBinding('role') button;
    @HostListener('mouseenter') onMouseEnter() {...}
  }
  ```

* Use element selectors for components.

  *Why?*: There could be only a single component per element.

  *Why?*: Components are the actual elements in our applications, compared to directives which only augment the elements.

* Use `kebab-case` for naming the element selectors of your components.

  *Why?*: Keeps the element names consistent with the specification for [Custom Elements](https://www.w3.org/TR/custom-elements/).

  ```ts
  /* WRONG */
  @Component({
    selector: '[sg-button]',
    template: `...`
  })
  class ButtonComponent {}
  ```

  ```ts
  /* CORRECT */
  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent {}
  ```

* Keep the components as simple and coherent as possible but not too simple.

  *Why?*: Simple coherent components are easier to reason about, more reusable and composable.

  *Why?*: Components which are too primitive may lead to scattering and harder management of the user interface of our applications.

* Keep the components' templates as lean as possible and inline them inside of the [`@Component`](https://angular.io/docs/ts/latest/api/core/Component-decorator.html) decorator.

  *Why?*: Keeping the components' templates short implies simple, composable components.

  *Why?*: Keeping the template next to the component's controller makes it easier to lookup the component's structure and/or modify it.

* Extract the more complex and bigger templates, longer than 15 lines of code, into a separate file and put them next to their controllers' definition.

  *Why?*: In case a big and complex template is inlined in the component metadata it may shift the focus from the component's logic defined within the controller.

* Locate the template of the component in the same directory where the component's logic resides in.

* Use [`@Input`](https://angular.io/docs/ts/latest/api/core/Input-var.html) and [`@Output`](https://angular.io/docs/ts/latest/api/core/Output-var.html) instead of the `inputs` and `outputs` properties of the [`@Directive`](https://angular.io/docs/ts/latest/api/core/Directive-decorator.html) and [`@Component`](https://angular.io/docs/ts/latest/api/core/Component-decorator.html) decorators:

  *Why?*: The name of the property, or event name associated to [`@Input`](https://angular.io/docs/ts/latest/api/core/Input-var.html) or respectively [`@Output`](https://angular.io/docs/ts/latest/api/core/Output-var.html) should be modified only on a single place.

  *Why?*: The metadata declaration attached to the directive is shorter and thus more readable.

  ```ts
  /* WRONG */
  @Component({
    selector: 'sg-button',
    template: `...`,
    inputs: [
      'label'
    ],
    outputs: [
      'change'
    ]
  })
  class ButtonComponent {
    change = new EventEmitter<any>();
    label: string;
  }
  ```

  ```ts
  /* CORRECT */
  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent {
    @Output()
    change = new EventEmitter<any>();
    @Input()
    label: string;
  }
  ```
* Do not rename inputs and outputs.

  *Why?*: May lead to confusion when the output or the input properties of a given directive are named a given way but exported differently as a public API.

  ```ts
  /* WRONG */
  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent {
    @Output('changeEvent') change = new EventEmitter<any>();
    @Input('labelAttribute') label: string;
  }
  /*
   * Need to be consumed the following way:
   *
   * <sg-button labelAttribute="foobar" (changeEvent)="doStuff()">
   * </sg-button>
   */
  ```

  ```ts
  /* CORRECT */
  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent {
    @Output() change = new EventEmitter<any>();
    @Input() label: string;
  }
  /*
   * Need to be consumed the following way, which is much more straightforward:
   *
   * <sg-button label="foobar" (change)="doStuff()">
   * </sg-button>
   */
  ```

* Prefer inputs over the [`@Attribute`](https://angular.io/docs/ts/latest/api/core/Attribute-var.html) parameter decorator.

  *Why?*: The input creates one-way binding which means that we can bind it to an expression and its value gets automatically updated. We can inject a property with [`@Attribute`](https://angular.io/docs/ts/latest/api/core/Attribute-var.html) to a controller's constructor and get its value a single time.

  ```ts
  /* WRONG */
  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent {
    label: string;
    constructor(@Attribute('label') label) {
      this.label = label;
    }
  }
  ```

  ```ts
  /* CORRECT */
  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent {
    @Input() label: string;
  }
  ```

* Detach components and directives which are not visible from the view in order to prevent the change detection running over them.

  ```ts
  /* CORRECT */
  @Directive({
    selector: '[pane]'
  })
  class Pane {
    @Input() title;
    view: EmbeddedViewRef;
    constructor(private _viewContainer: ViewContainerRef, private _templateRef: TemplateRef) {}
    // Creates and attaches the view
    show() {
      this.view = this._viewContainer.createEmbeddedView(this._templateRef);
      this._viewContainer.insert(this.view);
    }
    // Detaches the view container
    hide() {
      this._viewContainer.detach();
      this._viewContainer.remove();
    }
  }
  ```

* Do not use native elements injected to the controller's constructors with `ElementRef`.

  *Why?*: This way the application will get tightly coupled to the platform and thus won't be able to run independently from it. For instance, a web application injecting native DOM elements won't be able to run in WebWorker, nor be rendered on the server-side.

  ```ts
  /* WRONG */
  @Component({
    selector: 'sg-text-field',
    template: `<input type="text">`
  })
  class TextFieldComponent {
    value: string;
    constructor(el: ElementRef) {
      el.nativeElement.querySelector('input[type="text"]')
        .onchange = () => this.value = el.value;
    }
  }
  ```

  ```ts
  /* CORRECT */
  import {NgModel} from 'angular2/common';

  @Component({
    directives: [NgModel],
    selector: 'sg-text-field',
    template: `<input [(ngModel)]="value" type="text">`
  })
  class TextFieldComponent {
    value: string;
  }
  ```

* Keep the component templates logicless.

  *Why?*: Keeping the logic of the components in their controller, instead of template will bring a lot of benefits such as better testability, maintability, reusability.

* Do not manipulate element referenced within the template.

  *Why?*: This way the application will get tightly coupled to the platform and thus won't be able to run independently from it. For instance, a web application injecting native DOM elements won't be able to run in WebWorker, neither be rendered on the server-side.

  ```ts
  /* WRONG */
  @Component({
    selector: 'sg-items-list',
    template: `
      <input type="text" #textInput>
      <button (click)="add(textInput.value)">Add</button>
    `
  })
  class ItemListComponent {
    values: string[] = [];
    add(val) {
      this.values.push(val);
    }
  }
  ```

  ```ts
  /* CORRECT */
  import {NgModel} from 'angular2/common';

  @Component({
    directives: [NgModel],
    selector: 'sg-items-list',
    template: `
      <input type="text" [(ngModel)]="value">
      <button (click)="add()">Add</button>
    `
  })
  class ItemListComponent {
    value: string;
    values: string[] = [];
    add() {
      this.values.push(this.value);
    }
  }
  ```

* Always explicitly implement the interfaces associated with the used by given component life-cycle hooks.

  *Why?*: In case the interface associated to given life-cycle hook is implemented one will get compile-time errors in case the hook is not implemented properly (for instance, the method name is misspelled).

  ```ts
  /* WRONG */
  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent {
    ngOnInit {
      console.log('The component got initialized');
    }
  }
  ```

  ```ts
  /* CORRECT */
  import {OnInit} from 'angular2/core';

  @Component({
    selector: 'sg-button',
    template: `...`
  })
  class ButtonComponent implements OnInit {
    ngOnInit {
      console.log('The component got initialized');
    }
  }
  ```
  **[Table of Contents](#table-of-contents)**

## Services and Dependency Injection

* Limit the usage of [`forwardRef`](https://angular.io/docs/ts/latest/api/core/forwardRef-function.html).

  *Why?*: The usage of [`forwardRef`](https://angular.io/docs/ts/latest/api/core/forwardRef-function.html) indicates either a cyclic dependency or inconsistency in the services' declaration (i.e. the dependent service is declared before its dependency). In both cases there is usually a better approach to be used.

* Do not declare global providers in the [`bootstrap`](https://angular.io/docs/ts/latest/api/platform/browser/bootstrap-function.html) call, use a top-level component instead.

  *Why?*: The providers registered in the [`bootstrap`](https://angular.io/docs/ts/latest/api/platform/browser/bootstrap-function.html) method are meant for overriding existing providers rather than declaring dependencies.

  **[Table of Contents](#table-of-contents)**

## Pipes

* Name pipes' controllers with `Pipe` as suffix. The name of any pipe should be formed observing the *BasicDescription + Pipe* rule.

* Always implement the `PipeTransform` interface when building a Pipe.

  *Why?* This ensures your custom Pipes will conform to the framework requirements should they change in the future.

* Name your pipe `name` property in camelCase.

  ```ts
  /* WRONG */
  @Pipe({
    name: 'sg-transform-something'
  })
  class TransformSomething {
    transform(input: any): any {
      //...
    }
  }
  ```

  ```ts
  /* CORRECT */
  @Pipe({
    name: 'sgTransformSomething'
  })
  class TransformSomethingPipe implements PipeTransform {
    transform(input: any): any {
      //...
    }
  }
  ```

* Use [impure pipes](https://angular.io/docs/ts/latest/api/core/PipeMetadata-class.html) only when they need to hold state.

  *Why?*: The change detection can optimize pure pipes since they hold the [referential transparency](https://en.wikipedia.org/wiki/Referential_transparency) property.

* Wherever stateful (impure) pipes are required, implement the `OnDestroy` interface.

  *Why?* It is likely that a stateful pipe may contain state that should be cleaned up when a binding is destroyed. For example, a subscription to a stream of data may need to be disposed, or an interval may need to be cleared.

  **[Table of Contents](#table-of-contents)**

## Routing

* Name the routes the same way the components associated with them are called.

  *Why?*: This way there is only a single name associated to a given route. This avoids confusion in case a given route is called in a different way to the components associated with it.

  **[Table of Contents](#table-of-contents)**

## Forms

* Do not use expression for name of a control.

  *Why?*: When the name of the control is dynamically generated it will be hard to reference it inside of the controller associated to the form, and may lead to confusion.

  ```html
  /* WRONG */
  <form>
    <input [ngControl]="foobar" type="text">
  </form>
  ```
  ```ts
  @Component(...)
  class SampleComponent {
    foobar = 'foo';
  }
  ```

  ```html
  /* CORRECT */
  <form>
    <input ngControl="foo" type="text">
  </form>
  ```
  ```ts
  @Component(...)
  class SampleComponent {}
  ```
  **[Table of Contents](#table-of-contents)**

## Reusable libraries

* Follow the [angular-cli publisher guide](https://github.com/angular/angular-cli/blob/master/docs/ng-install.md#b-publisher-guide-preparing-your-library).

  *Why?*: It will allow your library to be painlessly used with the official Angular 2 CLI tool.

  **[Table of Contents](#table-of-contents)**

## Testing

* Use [Jasmine](https://jasmine.github.io/) for unit testing.

  *Why?*: It is well supported and popular in the JavaScript community.

  *Why?*: It is widely popular in the Angular community. Jasmine is used for testing both AngularJS 1.x and Angular 2.

  *Why?*: It has up-to-date type definitions so you can have great development experience using TypeScript.

* Use Karma as test runner.

* Place your test files side-by-side with your client code.

  *Why?*: The tests are easier to find since they are always next to the code they are testing.

  *Why?*: When you update source code it is easier to go update the tests at the same time.

  *Why?*: The code may act as documentation of the tested component.

* Name the test file the following way `NAME_OF_THE_TESTED_UNIT.spec.EXT`:

  ```
  /* CORRECT */
  about.ts
  about.spec.ts
  ```

* Place integration tests and tests that cover multiple code units into a separate `tests` directory in your project's root.

  **[Table of Contents](#table-of-contents)**

## Change detection

* Use [`OnPush`](https://angular.io/docs/ts/latest/api/core/ChangeDetectionStrategy-enum.html) change detection strategy for [pure/dumb components](http://teropa.info/blog/2015/10/18/refactoring-angular-apps-to-components.html#toward-smart-and-dumb-components) that accepts as input immutable data.

  *Why?*: Angular will optimize the performance of your application dramatically by not performing change detection over the entire sub tree with root the given pure component.

  **[Table of Contents](#table-of-contents)**

## TypeScript specific practices

* Use [TSLint](http://palantir.github.io/tslint/) for linting your code.

  *Why?*: In big teams, code following the same practices is easier to read and understand.

* Be as explicit in the type definitions as possible (i.e. use `any` as rarely as possible).

  *Why?*: `any` makes TypeScript behaves like a dynamic language. This way we gave up all the benefits we get from static typing such as better IDE/text editor support and compile-time type-checking.

* Use "Façade modules" for exporting the public members of your application/library and hiding the implementation details.

  ```ts
  /* Example */
  export {NgClass} from './directives/ng_class';
  export {NgFor} from './directives/ng_for';
  export {NgIf} from './directives/ng_if';
  export {NgStyle} from './directives/ng_style';
  export {NgSwitch, NgSwitchWhen, NgSwitchDefault} from './directives/ng_switch';
  export * from './directives/observable_list_diff';
  export {CORE_DIRECTIVES} from './directives/core_directives';
  ```

#### TypeScript dependency injection

* Use the [`@Injectable()`](https://angular.io/docs/ts/latest/api/core/Injectable-decorator.html) decorator instead of explicitly declaring the dependencies using [`@Inject(TOKEN)`](https://angular.io/docs/ts/latest/api/testing/inject-function.html).

  *Why?*: Using [`@Injectable()`](https://angular.io/docs/ts/latest/api/core/Injectable-decorator.html) the TypeScript compiler will emit the required type metadata, which makes the code using [`@Inject()`](https://angular.io/docs/ts/latest/api/testing/inject-function.html) unnecessary verbose and less readable.

### TSLint

* Use [TSLint](http://palantir.github.io/tslint/) for linting your JavaScript and be sure to customize the TSLint options file and include in source control. See the [TSLint docs](https://github.com/palantir/tslint) for details on the options.

  *Why?*: Provides a first alert prior to committing any code to source control.

  *Why?*: Provides consistency across your team.

  ```json
  {
    "rules": {
      "class-name": true,
      "curly": false,
      "eofline": true,
      "indent": "spaces",
      "max-line-length": [true, 140],
      "member-ordering": [true,
        "public-before-private",
        "static-before-instance",
        "variables-before-functions"
      ],
      "no-arg": true,
      "no-construct": true,
      "no-duplicate-key": true,
      "no-duplicate-variable": true,
      "no-empty": true,
      "no-eval": true,
      "no-trailing-comma": true,
      "no-trailing-whitespace": true,
      "no-unused-expression": true,
      "no-unused-variable": true,
      "no-unreachable": true,
      "no-use-before-declare": true,
      "one-line": [true,
        "check-open-brace",
        "check-catch",
        "check-else",
        "check-whitespace"
      ],
      "quotemark": [true, "single"],
      "semicolon": true,
      "triple-equals": true,
      "variable-name": false
    }
  }
  ```
  **[Table of Contents](#table-of-contents)**

## ES2015 and ES2016 specific practices



## ES5 specific practices

* Use the internal JavaScript DLS provided by Angular 2 for annotating constructor functions.

  *Why?*: This syntax is simpler, more readable and closer to the original TypeScript version of the code.

  *Why?*: The DLS uses function expressions which are closer to the non-hoisted ES2015 classes.

  ```js
  /* WRONG */
  function Component() {
    //...
  }
  Component.annotations = [
    new ng.core.ComponentMetadata({
      //...
    }),
    new ng.router.RouteConfig([
      //...
    ])
  ];
  ```

  ```ts
  /* CORRECT */
  var Component = ng.
    Component({
      //...
    })
    .RouteConfig([
      //...
    ])
    .Class({
      constructor: functions () {
        //...
      }
    });
  ```

### JSHint

* Use [JSHint](http://www.jshint.com/) for linting your JavaScript and be sure to customize the [JSHint](http://www.jshint.com/) options file and include in source control. See the [JSHint](http://www.jshint.com/docs/) docs for details on the options.

  *Why?*: Provides a first alert prior to committing any code to source control.

  *Why?*: Provides consistency across your team.

  ```json
  {
      "bitwise": true,
      "camelcase": true,
      "curly": true,
      "eqeqeq": true,
      "es3": false,
      "forin": true,
      "freeze": true,
      "immed": true,
      "indent": 2,
      "latedef": "nofunc",
      "newcap": true,
      "noarg": true,
      "noempty": true,
      "nonbsp": true,
      "nonew": true,
      "plusplus": false,
      "quotmark": "single",
      "undef": true,
      "unused": false,
      "strict": false,
      "maxparams": 10,
      "maxdepth": 5,
      "maxstatements": 40,
      "maxcomplexity": 8,
      "maxlen": 140,

      "asi": false,
      "boss": false,
      "debug": false,
      "eqnull": true,
      "esnext": false,
      "evil": false,
      "expr": false,
      "funcscope": false,
      "globalstrict": false,
      "iterator": false,
      "lastsemic": false,
      "laxbreak": false,
      "laxcomma": false,
      "loopfunc": true,
      "maxerr": false,
      "moz": false,
      "multistr": false,
      "notypeof": false,
      "proto": false,
      "scripturl": false,
      "shadow": false,
      "sub": true,
      "supernew": false,
      "validthis": false,
      "noyield": false,

      "browser": true,
      "node": true,

      "globals": {
          "angular": false,
          "ng": false
      }
  }
  ```

### JSCS

* Use [JSCS](http://jscs.info/) for checking your coding styles your JavaScript and be sure to customize the [JSCS](http://jscs.info/) options file and include in source control. See the [JSCS docs](http://jscs.info/rules) for details on the options.

  *Why?*: Provides a first alert prior to committing any code to source control.

  *Why?*: Provides consistency across your team.

  ```json
  {
      "excludeFiles": ["node_modules/**", "bower_components/**"],

      "requireCurlyBraces": [
          "if",
          "else",
          "for",
          "while",
          "do",
          "try",
          "catch"
      ],
      "requireOperatorBeforeLineBreak": true,
      "requireCamelCaseOrUpperCaseIdentifiers": true,
      "maximumLineLength": {
        "value": 140,
        "allowComments": true,
        "allowRegex": true
      },
      "validateIndentation": 2,
      "validateQuoteMarks": "'",

      "disallowMultipleLineStrings": true,
      "disallowMixedSpacesAndTabs": true,
      "disallowTrailingWhitespace": true,
      "disallowSpaceAfterPrefixUnaryOperators": true,
      "disallowMultipleVarDecl": null,

      "requireSpaceAfterKeywords": [
        "if",
        "else",
        "for",
        "while",
        "do",
        "switch",
        "return",
        "try",
        "catch"
      ],
      "requireSpaceBeforeBinaryOperators": [
          "=", "+=", "-=", "*=", "/=", "%=", "<<=", ">>=", ">>>=",
          "&=", "|=", "^=", "+=",

          "+", "-", "*", "/", "%", "<<", ">>", ">>>", "&",
          "|", "^", "&&", "||", "===", "==", ">=",
          "<=", "<", ">", "!=", "!=="
      ],
      "requireSpaceAfterBinaryOperators": true,
      "requireSpacesInConditionalExpression": true,
      "requireSpaceBeforeBlockStatements": true,
      "requireLineFeedAtFileEnd": true,
      "disallowSpacesInsideObjectBrackets": "all",
      "disallowSpacesInsideArrayBrackets": "all",
      "disallowSpacesInsideParentheses": true,

      "jsDoc": {
          "checkAnnotations": true,
          "checkParamNames": true,
          "requireParamTypes": true,
          "checkReturnTypes": true,
          "checkTypes": true
      },

      "disallowMultipleLineBreaks": true,

      "disallowCommaBeforeLineBreak": null,
      "disallowDanglingUnderscores": null,
      "disallowEmptyBlocks": null,
      "disallowTrailingComma": null,
      "requireCommaBeforeLineBreak": null,
      "requireDotNotation": null,
      "requireMultipleVarDecl": null,
      "requireParenthesesAroundIIFE": true
  }
  ```
  **[Table of Contents](#table-of-contents)**

## License

MIT
