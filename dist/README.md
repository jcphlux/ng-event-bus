# ng-event-bus

RxJS-based message/event bus service for Angular apps inspired by [NgRadio](https://github.com/govorov/ng-radio). Inject it in your application module. You can check [npm page](https://www.npmjs.com/package/ng-event-bus).

[![Build Status](https://travis-ci.com/cristiammercado/ng-event-bus.svg?branch=master)](https://travis-ci.com/cristiammercado/ng-event-bus)
[![npm version](https://badge.fury.io/js/ng-event-bus.svg)](https://badge.fury.io/js/ng-event-bus)

## Installation

`npm install --save ng-event-bus`

## Usage

First, import it:

`import { NgEventBus } from 'ng-event-bus';`

Then, inject it as a service (do not forget about providers) in your Angular application:

```
......
import { NgEventBus } from 'ng-event-bus';
......

@NgModule({
    imports:[
    	.......
    ],
    providers: [
    	.......
        NgEventBus,
        .......
    ],
```

`constructor(private eventBus: NgEventBus){...}`

Since you have `NgEventBus` instance in your app, you can use these methods for passing messages:

* `this.eventBus.cast(key, data)` - send a message to event bus.

* `this.eventBus.on(pattern)` - returns observable you can subscribe to listen events.

Patterns may contain multiple segments split by `:`. Use this feature to create namespaces for messages you cast. You can use `*` in `pattern` to subscribe to any matching segment, or use `**` to subscribe to all segments, starting from particular position.

For example, you can use `on('error:*')` and subscribe to all errors, including something like `error:http` or `error:internal` and so on:

```
this.eventBus.cast('app:start',     'started');
this.eventBus.cast('message:greet', 'Hi!');
this.eventBus.cast('message:bye',   'Bye!');

this.eventBus.on('app:start').subscribe((message)=>{
	console.log(message); //will receive 'started' only
});

this.eventBus.on('message:greet').subscribe((message)=>{
	console.log(message); //will receive 'Hi!'
});

this.eventBus.on('message:bye').subscribe((message)=>{
	console.log(message); //will receive 'Bye!'
});

this.eventBus.on('message:*').subscribe((message)=>{
	console.log(message); //will receive both 'Hi!' and 'Bye!'
});

this.eventBus.on('**').subscribe((message)=>{
	console.log(message); //will receive all messages: 'started', 'Hi!' and 'Bye!'
});

```

### Examples

These strings will match:

* `on('**'    , callback)` can subscribe to any message with any segments count

* `on('a'     , callback)` can subscribe to `cast('a', ...)`

* `on('a:b'   , callback)` can subscribe to `cast('a:b', ...)`

* `on('a:b:c' , callback)` can subscribe to `cast('a:b:c', ...)`

* `on('a:**'  , callback)` can subscribe to `cast('a:b:c', ...)`, `cast('a:b:c:d:e:f', ...)`

* `on('a:*:*' , callback)` can subscribe to `cast('a:b:c', ...)`, `cast('a:f:g', ...)`, `cast('a:n:m', ...)`

* `on('a:b:*' , callback)` can subscribe to `cast('a:b:c', ...)`, `cast('a:b:d', ...)`, but not `cast('a:b', ...)`

* `on('a:b:**', callback)` can subscribe to `cast('a:b:c',. ..)`

* `on('*:b:*' , callback)` can subscribe to `cast('a:b:c', ...)`

* `on('a:*:*' , callback)` can subscribe to `cast('a:b:c', ...)`

### Need to unsubscribe (observable)?

Yes, in the normal scenario usage it's necessary you unsubscribe from the observable to avoid memory leaks. That happens because the library exposes an infinite observable. That's no exactly related to the library, but in the way in which the observables work in RxJS.

However, there are ways that you don't need to unsubscribe, for example if you use `.first()` in the pipe of the observable because using this method would turn it into a finite observable (It would apply in your case if you're just waiting for a one-time event).

I recommend you to read this [stackoverflow answer](https://stackoverflow.com/questions/50629357/rxjs-angular-unsubscribe-from-subjects/50633482#50633482) about a similar question.

## Release History & Changelog

See the [Releases](https://github.com/cristiammercado/ng-event-bus/releases) page for a list of all releases, including changes.

## Help / Support

If you run into any issues, please email me at [contact@cristiammercado.com](mailto:contact@cristiammercado.com) or you can use the contact form in [my page](https://www.cristiammercado.com/en/#contact).

For bug reports, please [open an issue on GitHub](https://github.com/cristiammercado/ng-event-bus/issues/new).

## Contributing

1. [Fork it](https://github.com/cristiammercado/ng-event-bus).
2. Create your feature branch (`git checkout -b my-new-feature`).
3. Commit your changes (`git commit -am 'Added some feature'`).
4. Push to the branch (`git push origin my-new-feature`).
5. Create a new Pull Request.

## License

[MIT](https://github.com/cristiammercado/ng-event-bus/blob/master/LICENSE)