## Question
I was needed to pass a generic number of actions from controller scope to component scope and execute them without additional JS code in a component.

## Step #1
The first and the most straightforward idea was this:

### Code
```javascript
// controller.js
export default Ember.Controller.extend({
    actions: {
        foo1() {
            // ...
        },
        foo2() {
            // ...
        }
    }
});

// controller.hbs
{{component foo1=(action 'foo1') foo2=(action 'foo2')}}

// component.js
export default Ember.Component.extend({
   actions: {
       foo1() {
           this.sendAction('foo1');
       },
       foo2() {
           this.sendAction('foo2');
       }
   }
});

// component.hbs
<div {{action 'foo1'}}>foo1</button>
<div {{action 'foo2'}}>foo2</button>
```

**Cons**: every method should be passed to component and an ugly `this.sendAction()` should be invoked for every this method. It means that every component will contain a lot of dummy code just to propagate method up to the controller.

## Step #2
I decided to implement generic method for action propagation from component and update its template:

```javascript
// component.js
export default Ember.Component.extend({
    actions: {
        sendAction(name) {
            this.sendAction(name);
        }
    }
});

// component.hbs
<div {{action 'sendAction' 'foo1'}}>foo1</button>
<div {{action 'sendAction' 'foo2'}}>foo2</button>
```
**Cons**:every component should have this method for propagation.

## Step #3
Mixin can help to avoid this kind of code duplication:
```javascript
// action-proxy-mixin.js
export default Ember.Mixin.create({ 
    actions: { 
        sendAction(name) { 
            this.sendAction(name); 
        } 
    } 
});

// component.js
import ActionProxyMixin from '/mixins/action-proxy-mixin.js'
export default Ember.Component.extend({
    actions: {
        sendAction(name) {
            this.sendAction(name);
        }
    }
});
```
**Cons**:still don't have a clear solution without ugly code.

## Step #4 (final)
After some googling, I have found this [article][link_article] and it has filled my missed knowledge about how to work with actions in EmberJS. The main idea is that we can use action closures instead of `this.sendAction()` and pass actions directly to `{{action myAction}}` helper. So we wipe up the mixin and use built-in action propagation. Here is the final code:
// controller.js
export default Ember.Controller.extend({
    actions: {
        foo1() {
            // ...
        },
        foo2() {
            // ...
        }
    }
});

// controller.hbs
{{component foo1=(action 'foo1') foo2=(action 'foo2')}}

// component.js
export default Ember.Component.extend({
   foo1: null,
   foo2: null
});

// component.hbs
<div {{action foo1}}>foo1</button>
<div {{action foo2}}>foo2</button>
```

Note: To bypass router action to controller you can use this [helper][link_router_action]. Example:
```javascript
// router.hbs
{{component foo1=(router-action 'foo1') foo2=(router-action 'foo2')}}
```

[link_article]: https://emberigniter.com/send-closure-actions-up-data-owner/
[link_router_action]: https://github.com/DockYard/ember-route-action-helper
