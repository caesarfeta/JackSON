#  JackSON App development
## Quick start
To create a JackSON app with AngularJS and Foundation boilerplate code...

[Install NodeJS](http://nodejs.org/)

Install required command line tools.

	rake install:ui

Create a JackSON app in **public/apps/name**

	rake app:make['name']

The url to your app will be...

	http://localhost:4567/apps/name

Keep your terminal open so **rake app:make** can listen for changes to your .scss files.

## Go deeper...
### Questions to ask yourself before beginning.

1. What data needs gathering?
2. What data needs to be searchable?

The answers to these questions will affect the flavor and design of your JSON.

If you don't need your data searchable just use JSON.

	{
	  "name": "Manuel Surly",
	  "homepage": "http://manuel.surly.org/",
	  "avatar": "http://twitter.com/account/profile_image/manuelsurly",
	}

To make this data SPARQL searchable use JSON-LD by adding **@context:{}**.
Then define each property with a RDF verb definition URL.

	{
	  "@context": {
	    "name": "http://xmlns.com/foaf/0.1/name",
	    "homepage": "http://xmlns.com/foaf/0.1/homepage",
	    "avatar": "http://xmlns.com/foaf/0.1/avatar"
	  },
	  "name": "Manuel Surly",
	  "homepage": "http://manuel.surly.org/",
	  "avatar": "http://twitter.com/account/profile_image/manuelsurly"
	}

This JSON-LD posted to **rdf/ld** produces these RDF triples.

	<http://localhost:4567/data/rdf/ld> <http://xmlns.com/foaf/0.1/avatar> "http://twitter.com/account/profile_image/manuelsurly"
	<http://localhost:4567/data/rdf/ld> <http://xmlns.com/foaf/0.1/homepage> "http://manuel.surly.org/"
	<http://localhost:4567/data/rdf/ld> <http://xmlns.com/foaf/0.1/name> "Manuel Surly"

Any property in the JSON which is not defined in @context will not produce an RDF triple.
Which means this JSON-LD...

	{
	  "@context": {
	    "name": "http://xmlns.com/foaf/0.1/name",
	    "homepage": "http://xmlns.com/foaf/0.1/homepage",
	    "avatar": "http://xmlns.com/foaf/0.1/avatar"
	  },
	  "name": "Manuel Surly",
	  "homepage": "http://manuel.surly.org/",
	  "avatar": "http://twitter.com/account/profile_image/manuelsurly",
	  "extra": "What happens when something extra is sent along?"
	}

which has a property "extra" not defined in @context, when posted to **rdf/ld** produces the same RDF as the JSON-LD without "extra"...

	<http://localhost:4567/data/rdf/ld> <http://xmlns.com/foaf/0.1/avatar> "http://twitter.com/account/profile_image/manuelsurly"
	<http://localhost:4567/data/rdf/ld> <http://xmlns.com/foaf/0.1/homepage> "http://manuel.surly.org/"
	<http://localhost:4567/data/rdf/ld> <http://xmlns.com/foaf/0.1/name> "Manuel Surly"

"extra" values will not be searchable by SPARQL, but can be retrieved pretty easily.  
Since the subject of each triple is a URL to the RDF's source JSON-LD file, 
making a GET request to **http://localhost:4567/data/rdf/ld** will allow you to retrieve any "extra" properties not accessible in RDF.

You may be wondering why you would ever retrieve source JSON-LD.  
Why not just use RDF returned by SPARQL queries?  
The answer is, "You'll eventually need a list."

### RDF and Lists
RDF can't easily preserve the sequence of items in a list, 
which can cause a lot of frustration.

For example pretend we are building a job application system.
We want to store an applicant's last three employers in chronological order.
We can do this easily with JSON-LD

	{
	  "@context": {
	    "name": "http://xmlns.com/foaf/0.1/name",
		"employers": "http://xmlns.com/foaf/0.1/organization"
	  },
	  "name": "Manuel Surly",
	  "employers": [ "Smallville Gazette", "Burger Barn", "Bruno's Bar & Grill" ]
	}

When this JSON-LD is posted to **rdf/ld_list** it produces RDF like this...

	<http://localhost:4567/data/rdf/ld_list> <http://xmlns.com/foaf/0.1/name> "Manuel Surly"
	<http://localhost:4567/data/rdf/ld_list> <http://xmlns.com/foaf/0.1/organization> "Burger Barn"
	<http://localhost:4567/data/rdf/ld_list> <http://xmlns.com/foaf/0.1/organization> "Smallville Gazette"                               
	<http://localhost:4567/data/rdf/ld_list> <http://xmlns.com/foaf/0.1/organization> "Bruno's Bar & Grill" 

The original sequence of employers is not encoded in these RDF triples,
which means queries might return these triples in any permutation.

	<http://localhost:4567/data/rdf/ld_list> <http://xmlns.com/foaf/0.1/organization> "Burger Barn"
	<http://localhost:4567/data/rdf/ld_list> <http://xmlns.com/foaf/0.1/organization> "Bruno's Bar & Grill" 
	<http://localhost:4567/data/rdf/ld_list> <http://xmlns.com/foaf/0.1/organization> "Smallville Gazette"                               

Trying to preserve order in RDF is hard.
[Smart people are working on it.](http://infolab.stanford.edu/~stefan/daml/order.html)

I think any solution to the problem is going to be cumbersome for developers to use anyway, 
so if you don't want to deal with those headaches just GET the source JSON-LD, and find the items sequence in the array.

### Literals and URIs
By default JSON-LD will have its value nodes converted to an RDF-Literal not a URI.
For example when this JSON-LD...

	{
		"@context": {
			"word": "http://localhost:4567/apps/lexinv/spec.html#word",
			"synonyms": "http://localhost:4567/apps/lexinv/spec.html#synonym"
		},
		"word": "cheesy",
		"synonyms": [
			"http://localhost:4567/data/rdf/ld_id/tacky", 
			"http://localhost:4567/data/rdf/ld_id/cheap", 
			"http://localhost:4567/data/rdf/ld_id/corny", 
			"http://localhost:4567/data/rdf/ld_id/cornball"
		]
	}

is POSTed to **rdf/cheesy** it produces this RDF...

	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#synonym> "http://localhost:4567/data/rdf/tacky"
	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#synonym> "http://localhost:4567/data/rdf/cheap"
	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#synonym> "http://localhost:4567/data/rdf/corny"
	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#synonym> "http://localhost:4567/data/rdf/cornball"
	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#word>    "cheesy"

The synonym URLs are stored as RDF string literals not URIs.

	"http://localhost:4567/data/rdf/tacky" != <http://localhost:4567/data/rdf/tacky>

They should be stored as URIs because they refer to resources similar to `<http://localhost:4567/data/rdf/cheesy>`,
and without going into too much detail there are privileges enjoyed by URIs that are not extended to RDF string literals.

Here's the JSON-LD slightly modified to produce the URIs we want...

	{
		"@context": {
			"word": "http://localhost:4567/apps/lexinv/spec.html#word",
			"synonyms": {
				"@id": "http://localhost:4567/apps/lexinv/spec.html#synonym",
				"@type": "@id"
			}
		},
		"word": "cheesy",
		"synonyms": [
			"http://localhost:4567/data/rdf/ld_id/tacky", 
			"http://localhost:4567/data/rdf/ld_id/cheap", 
			"http://localhost:4567/data/rdf/ld_id/corny", 
			"http://localhost:4567/data/rdf/ld_id/cornball"
		]
	}

and here is the RDF it creates...

	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#synonym> <http://localhost:4567/data/rdf/ld_id/tacky>
	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#synonym> <http://localhost:4567/data/rdf/ld_id/cheap>
	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#synonym> <http://localhost:4567/data/rdf/ld_id/corny>
	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#synonym> <http://localhost:4567/data/rdf/ld_id/cornball>
	<http://localhost:4567/data/rdf/cheesy> <http://localhost:4567/apps/lexinv/spec.html#word>    "cheesy"

### Validate uploaded JSON
	**Coming Soon!**

Validate uploaded JSON to ensure it adheres to a defined standard.
This is easily done with JackSON.

Create the same directory structure as the saved JSON file.
For example, data saved to...

	data/apps/my-app/save/data.json

...will be validated by...

	validate/apps/my-app/save/validate.json

Here's what **data/apps/my-app/save/data.json** looks like...

	{ 
		"@context": {
			"word": "http://sample.org/rdf/spec.html#word",
			"examples": "http://sample.org/rdf/spec.html#example",
			"syns": {
				"@id": "http://sample.org/rdf/spec.html#synonym",
				"@type": "@id"
			}
		},
		"word": "sample",
		"examples": [ "Would you like to taste a sample? Free of charge." ],
		"syns": [ "http://sample.org/data/example", "http://sample.org/data/example/model" ]
	}

Here's what **validate/apps/my-app/save/validate.json** looks like...

	{ 
		"@context": {
			"word": "http://sample.org/rdf/spec.html#word",
			"examples": "http://sample.org/rdf/spec.html#example",
			"syns": {
				"@id": "http://sample.org/rdf/spec.html#synonym",
				"@type": "@id"
			}
		},
		"word": { "type":"String" },
		"examples": { "type":"Array" },
		"syns": {
			"type":"Array",
			"regex":"/^http:\/\/sample.org\/data\//"
		}
	}

validate.json's @context key must match data.json explicitly.
The other keys map to objects that contain two possible values.

* type
	* Acceptable values: [ "String", "Integer", "Float", "Array" ]
* regex

If values in **data.json** do not match the type or fail the regex check it will not be saved and the server will return an ERROR HTTP code.

If a key-value pair exists in **data.json** that is not in **validate.json** it will still be saved and the server will return a SUCCESS HTTP code, but you will receive a warning in the SUCCESS JSON object.

### /urn?cite="urn:cite:organization:elem.1"
If you want an identifier different from the URL to a JSON-LD file
include the "JackSON urn" verb-value pair in your JSON-LD like this...

	{
	  "@context": {
	    "name": "http://localhost:4567/apps/elem/schema#name",
	    "symbol": "http://localhost:4567/apps/elem/schema#symbol",
	    "mass": "http://localhost:4567/apps/elem/schema#mass",
	    "number": "http://localhost:4567/apps/elem/schema#number",
	    "urn": "http://github.com/caesarfeta/JackSON/docs/SCHEMA.md#urn"
	  },
	  "urn": "urn:cite:organization:elem.1",
	  "name": "Hydrogen",
	  "symbol": "H",
	  "mass": "",
	  "number": "1"
	}

Then you can retrieve the JSON-LD file using a URN like this...

	http://localhost:4567/urn?cite="urn:cite:organization:elem.1"

	**Coming Soon!**

A JackSON server can search other JackSON instances to resolve a URN.
List these "family" JackSON instances in **JackSON.config.yml**.

	family: [ 'http://other.jackson-server.org', 'http://another.jackson-server.org' ]


### /query?query=SELECT...
You can run queries on the endpoint connected to JackSON by passing the SPARQL query as a URL escaped string.  Like this...

	http://localhost:4321/ds/query?query=SELECT%20?p%20?o%20WHERE%20%7B%20%3Chttp://localhost:4567/data/elem/1%3E%20?p%20?o%20%7D

### ?cmd=ls
You can run commands on **/data/\*** URLs.
For example if you want to see all JSON files in a URL directory you can run... 

	http://localhost:4567/apps/elem?cmd=ls	

...which will return JSON like this...

	{
	  "dirs": [],
	  "files": [
	    "http://127.0.0.1:4567/data/elem/h",
	    "http://127.0.0.1:4567/data/elem/he",
	    "http://127.0.0.1:4567/data/elem/li"
	  ]
	}
