## Background
EmberJS by default has `rollbackAttributes` method that rollbacks model attributes, but does nothing with its `belongsTo` and `hasMany` relations. Alos it applies to `changedAttribute` method that returns only model's changes attributes without relations.

## Question
How to rollback model attributes with all relations?

## Answer
There is an awesome EmberJS [plugin][link_plugin] that enables this ability for any model. It has a lot of options and can be configured to fit any requirement.

## One more thing
But there is one more thing that does not allow to use this plugin as expected. By default Ember `findRecord` method returns records from the cache if it exists and in a background makes an API call. This behavior breaks the plugin because it can be that `query` method returns models without relations (API can be designed in this way) and `findRecord` method returns fully loaded model with all relations. In this case, if `findRecord` method was called right after `query` method EmberJS will return model from the cache, start API call and will not fire any `ready` or `didUpdate` model triggers after call completion so nobody knows that the model has been updated in a background. This behavior makes stored by plugin relations broken and makes the model 'dirty'.

## Solution
Override store `findRecord` method and make an additional plugin call:
```javascript
export default DS.Store.extend({
    findRecord(name, id, options) {
        options = Object.assign({ reload: true }, options);

        return this._super(name, id, options).then((record) => {
            record.startTrack();

            return record;
        });
    }
});
```

As an option `options = Object.assign({ reload: true }, options);` can be moved to an adapter:
```javascript
export default DS.RESTAdapter.extend(DataAdapterMixin, {
	shouldReloadRecord() {
		return true;
	},
});
```

This trick forces EmberJS to do not use cache for `findRecord` method, fire model `ready` trigger when model was loaded from the API and let plugin know that model was updated.

[link_plugin]: https://github.com/danielspaniel/ember-data-change-tracker/
