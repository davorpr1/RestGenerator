RestGenerator
=============

RestGenerator is a DSL package (a plugin module) for [Rhetos development platform](https://github.com/Rhetos/Rhetos).
It automatically generates **RESTful JSON web service** for all entities, actions and other data structures that are defined in a Rhetos application.

See [rhetos.org](http://www.rhetos.org/) for more information on Rhetos.

Features
--------

###Web service methods

For each data structure, a service is available at `<rhetos server url>/Rest/<module name>/<entity name>`.

Example - a service for entity Claim in module Common, on default local server installation:

* Base service URI (reading service metadata): `http://localhost/Rhetos/Rest/Common/Claim`
* Reading all entity's records: `http://localhost/Rhetos/Rest/Common/Claim/` (don't forget the *slash* at the end)

Following are URI templates for the web methods.

**Reading data:**

* Reading records: `/?filters={{filters}}&top={{top}}&skip={{skip}}&sort={{sort}}`
	* For filters, see *Filters* paragraph below.
	* Example of sorting by multiple properties: `sort=CreationDate desc,Name,ID`.
* Reading total records count for paging: `/TotalCount?filters={{filters}}&sort={{sort}}`
* Reading records and total count: `/RecordsAndTotalCount?filters={{filters}}&top={{top}}&skip={{skip}}&sort={{sort}}`
* Reading a single record: `/{{id}}`

**Writing data:**

* Inserting a record: POST at the entity's service base URI.
* Updating a record: PUT `/{{id}}`
* Deleting a record: DELETE `/{{id}}`

**Actions:**

* Executing an action: POST at the action's service base URI.

**Reports:**

* Downloading a report: `/?parameter={{parameter}}&convertFormat={{convertFormat}}`
	* Query parameters `parameter` and `convertFormat` are optional.
	* Example format `http://localhost/Rhetos/Rest/TestModule/TestReport/?parameter={"Prefix":"a"}&convertFormat=pdf`

###Filters

Filters are given as a JSON-serialized array containing any number of filters of the following types:

1. **Property filter**
	* Example: select items where year is greater than 2005: `[{"Property":"Year","Operation":"Greater", "Value":2005}]`
	* Available operations:
        * `Equal`, `NotEqual`, `Greater`, `GreaterEqual`, `Less`, `LessEqual`,
        * `StartsWith`, `Contains` -- String only,
        * `DateIn` -- Date or DateTime property only, provided value must be string.
          Returns whether the property's value is within a given day, month or year.
          Valid value format is *yyyy-mm-dd*, *yyyy-mm* or *yyyy*.
2. **Predefined filter** without a parameter
	* Example: select active records (filter name: "Common.Active"): `[{"Filter":"Common.Active"}]`
3. **Predefined filter** with a parameter
	* Example: select records that contain pattern "abc" (filter name: "Common.SmartSearch" with parameter property "Pattern"): `[{"Filter":"Common.SmartSearch","Value":{"Pattern":"abc"}}]`

When combining multiple filters, the intersection of the filters is returned (AND).

###Obsolete features

These features are available for backward compatibility. They will be removed in future versions. 

* `/Count` WEB API method. Use `/TotalCount` method instead.
* Reading method query parameters `page` and `psize`. Use `top` and `skip`.
* Reading method query parameters `filter` and `fparam`. Use `filters` instead (see "Predefined filter with a parameter").
* Reading method query parameter `genericfilter`. Renamed to `filters`.

Deployment
----------

### Prerequisites

* *CommonConcepts* package must be deployed along with *RestGenerator*.

Building binaries from source
-----------------------------

### Prerequisites

* Build utilities in this project are based on relative path to Rhetos repository.
  [Rhetos source](https://github.com/Rhetos/Rhetos) should be downloaded to a folder
  with relative path `..\..\Rhetos` and compiled (use `Build.bat`),
  before this package's `Build.bat` script is executed.

Sample folder structure:
 
	\ROOT
		\Rhetos
		\RhetosPackages
			\RestGenerator

### Build

1. Build this package by executing `Build.bat`. The script will pause in case of an error.
   * The script automatically copies all needed dll files from Rhetos folder and builds the Rhetos.RestGenerator.sln using Visual Studio (command-line).

### Create installation package

1. Set the new version number in `ChangeVersion.bat` and execute it.
2. Execute `Build.bat`.
3. Execute `CreatePackage.bat`. It creates installation package (.zip) in parent directory of RestGenerator.
