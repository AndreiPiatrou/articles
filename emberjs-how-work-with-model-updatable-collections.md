## Use case
Usually, you work with EmberJS Data Model collections and have to display some list of entities, create, edit and delete them in different routes, for example, there is a table in one root route and some child modal route that should create/edit/delete entities.

## Question
How to organize the model collection, that should be binded on the view and updated outside of parent route without model reload?

## Variant #1
Create a `service`, with, for example, `store` method:
```js
// users-store-service.js
export default Ember.Service.extend({
    users: null,
    save(users) {
        this.set('users', users.toArray()); // make sure that usual array will be stored instead of data store collection

        return this.get('users');
    },
    add(user) {
        this.get('users').addObject(user);
    },
    delete(user) {
        this.get('users').removeObject(user);
    }
});
```
and use this implementation like following:
```js
// route.js
export default Ember.Route.extend({
    usersStoreService: Ember.inject.service(),
    model() {
        return Ember.RSVP.hash({
            users: this.store.findAll().then((users) => this.get('usersStoreService').save(users))
        })
    }
});

// controller.js
export default Ember.Controller.extend({
    usersStoreService: Ember.inject.service()
});

// user-create-controller.js
export default Ember.Controller.extend({
    usersStoreService: Ember.inject.service(),
    actions: {
        save() {
            this.get('model').save().then((user) => {
                this.get('usersStoreService').add(user);
                this.transitionTo('users');
            };
        }
    }
});
```
```hbs
// users.hbs
{{table records=model.users }}
```
Since all routes, controllers and services are singletons - ti allows us to do such things. But in this case we have to create a lot of addition code, maintain it, cover by tests and so on.

## Varian #2
Use `RecordsArray` and `peekAll` method:
```js
// route.js
export default Ember.Route.extend({
	beforeModel() {
		this._super(...arguments);
		this.store.unloadAll('users');
	},
	model() {
		return Ember.RSVP.hash({
			// here you can use any query method too.
			users: this.store.findAll('users').then(() => this.store.peekAll('users'))
		});
	}
});

//controller.js
export default Ember.Controller.extend({
    users: Ember.computed('model.users.@each.isNew', function() {
        // here you can place any filter/sort logic that you want to perform on the client side.
        return this.get('model.users');
    })
});
```
```hbs
// users.hbs
{{table records=users }}
```
`RecordsArray` is an array from the `store` and this array gets updated on `record.destroyRecord()` and `record.save()` actions, so your view and computed properties will be updated and you do not need to take care of manual `model` reload or something else.
