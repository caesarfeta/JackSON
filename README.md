# JackSON
JackSON is a lightweight server and Javascript API that will **create/POST**, **retrieve/GET**, **update/PUT**, and **delete/DELETE** JSON files RESTfully.

JackSON can also convert JSON-LD files to Fuseki served RDF automatically.
See [JackRDF](http://github.com/caesarfeta/jackrdf).

It was designed specifically for rapidly prototyping linked-data web applications with save and search capabilities.

## Install
[Install JackRDF](http://github.com/caesarfeta/jackrdf)

Install JackSON

	rake install:min

## Start
	rake server:start

## CORS
Applications that use JackSON can be hosted anywhere with one "gotcha", to communicate with the JackSON server from an application on another host you will need to update **JackSON.config.yml**

	cors: [ 'http://localhost:4567', 'http://localhost:3000', 'http://your.app-host.com' ]

## Create a JackSON backed AngularJS app
See **docs/APP.md**

## Contribute
If you would like to contribute code to this project see **docs/DEVELOP.md** for guidance.

## Useful Reading
* Manu Sporny talks about the relationshipe between [JSON-LD &amp; RDF](http://manu.sporny.org/2014/json-ld-origins-2/)
* [JSON-LD 1.0 W3C Recommendation](http://www.w3.org/TR/json-ld/)
* [JSON-LD RDF API Spec](http://json-ld.org/spec/latest/json-ld-rdf/)