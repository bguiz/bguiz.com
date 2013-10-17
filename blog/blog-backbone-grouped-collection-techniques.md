<!-- BackboneJs Grouped Collection Techniques - Four Approaches -->
<!-- javascript, backbonejs -->

	var Foo = Backbone.Model.extend({
		/* some code here */
	});

	//Default sollection
	Foo.Collection =  Backbone.Collection.extend({
		model: Foo
		/* some code here */
	});

	//# alt 1 - upfront, best when updates don't occur very often, and access occurs very often
	Foo.Collection =  Backbone.Collection.extend({
		model: Foo,
		initialize: function(initialModels, options) {
			this.listenTo(this, 'add remove sync', this._regroup);
			this._groupedData = [];
		},
		_regroup: function() {
			//TODO parse collection and group by bar
		},
		getGroupedModels: function() {
			return this._groupedData;
		}
	});

	//# alt 2 - lazy, best when updates occur very often, and access occurs not as often
	Foo.Collection =  Backbone.Collection.extend({
		model: Foo,
		initialize: function(initialModels, options) {
			this._groupedData = [];
		},
		_regroup: function() {
			//TODO parse collection and group by bar
		},
		getGroupedModels: function() {
			this._regroup();
			return this._groupedData;
		}
	});

	//# alt 3 - optimised, uses a flag to track whether updates have occurred
	Foo.Collection =  Backbone.Collection.extend({
		model: Foo,
		initialize: function(initialModels, options) {
			this.listenTo(this, 'add remove sync', this._flagForRegroup);
			this._groupedData = [];
			this._needsRegroup = true;
		},
		_flagForRegroup: function() {
			this._needsRegroup = true;
		},
		_regroup: function() {
			this._needsRegroup = false;
			//TODO parse collection and group by bar
		},
		getGroupedModels: function() {
			if (this._needsRegroup) {
				this._regroup();
			}
			return this._groupedData();
		}
	});

	//# alt 4 - use a separate collection. useful when data needs to be a backbone collection instead of POJSO array
	Foo.GroupedCollection = Backbone.Collection.extend({
		initialize(models, options) {
			this._origCollection = options.origCollection;
			this.listenTo(this._origCollection, 'add remove sync', this._regroup);
		},
		_regroup: function() {
			//parse this._origCollection and group by bar
		}
	});
