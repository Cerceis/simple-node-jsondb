### Features

- Extremely lightweight and simple local json database for personal/small/test projects.
- Capable of find, insert, update, delete
- **Targeting deep Object does not work. You need to manipulate the data manually and save it as whole.**

# simple-node-jsondb

## Installation
`$ npm install simple-node-jsondb@latest`

- Features
- Installation
- API
    - Example Dataset
- db.init([dbPath])
- db.insert([collection/table], [data])
    - Insert one
    - Insert many
- db.find([collection/table], [filter])
    - Find All
    - Find one
    - Multiple filters
    - Advance Search
- db.update([collection/table], [filter], [newData])
    - Update All records
    - Update One
- db.delete([collection/table], [filter], [confirm])
    - Delete one specific record
    - Delete records with conditions
    - Delete All
- Special Operators

# API
You can either use api as: 

	db.find().then((result)=>{
		/*Your logic*/
	}).catch(()=>{
		/*Error handle*/
	})
	
or
***Recommended

	try{
		let result = db.find()
		/*Your logic*/
	}catch(err){
		/*Error handle*/
	}

## Example Dataset
***Data will change accordingly throughout the documentation.***
Automatically start as an empty object

	{}

# db.init([dbPath])
To use the db, you need to initialize the db onload before you can use it, **by default the db path will be in the node_module folder** (`<path>`/node_modules/simpleJsonDB).
Simply pass in the desired absolute path to change the directory.

	/*Recommend to use "path"*/
	const path = require("path")
	/* This will initialize the db in your root project folder */
	const dbPath = path.join(__dirname,'./db') 
	db.init( dbPath )

# db.insert([collection/table], [data])
- Insert data into database.
- Refer as "collection" for noSQL users, or "table" for users that is more fimilar with SQL.

Params  | Required |  Type | Description
------------- | -------------
collection/table  | True |  String | Target collection/table
data | True  | Object/Array  | Data to insert into collection/table. Can be either **Object** or **Array**.

Return
- None

## Insert one
	
	const newUser = {
		"name":"Alice",
		"age":15,
		"food": [
			"Orange","Fish"
		],
	}
	/*Omitting async handles*/
	/* New collection/table "users" will be added automatically */
	await db.insert( 'users', newUser )
	
	let result = await db.find( 'users' , {} )
	console.log(result)
	{
		"users":[
			{
				"name":"John",
				"age":20,
				"food": [
					"Burger","Sushi"
				]
			}
		]
	}
	

## Insert many
By using Array

	const newUsers = [
		{
			"name":"Susan",
			"age":21,
			"food": [
				"Salad","Apple"
			],
		},
		{
			"name":"Alice",
			"age":15,
			"food": [
				"Orange","Fish"
			]
		}
	]
	/*Omitting async handles*/
	await db.insert( 'users', newUsers )
	
	let result = await db.find( 'users' , {} )
	console.log(result)
	{
		"name":"John",
		"age":20,
		"food": [
			"Burger","Sushi"
		],
	},
	{
		"name":"Susan",
		"age":21,
		"food": [
			"Salad","Apple"
		],
	},
	{
		"name":"Alice",
		"age":15,
		"food": [
			"Orange","Fish"
		]
	}

# db.find([collection/table], [filter])
- Find and retrieve data from database

Params  | Required |  Type | Description
------------- | -------------
collection/table  | True |  String | Target collection/table
filter | True | Object/Array  | Filter. Can be either **Object** or **Array**. ***Pass an empty Object to find all***

Return
- Array

Basic usage
## Find All

	let result = await db.find( 'users' , {} )

## Find one
Finding a single record.

	/* Omitting async handles*/
	let result = await db.find( 'users' , { name : "John" } )
	console.log(result)
	/* Result:
		[{
			"name":"John",
			"age":20,
			"food": [
				"Burger","Sushi"
			]
		}]
	*/

## Multiple filters
Finding with multiple `AND` conditions by using **Array**.

	/* Omitting async handles*/
	let result = await db.find( 'users' , [ { name : "John" }, { age : 20 } ] )
	console.log(result)
	/* Result:
		[{
			"name":"John",
			"age":20,
			"food": [
				"Burger","Sushi"
			]
		}]
	*/

## Advance Search
Advanced search with filtering can be done by using ***Special Operators***. 
***Check out Special Operators section in the end of the page for more info.***

	/* Omitting async handles*/
	let result = await db.find( 'users' , {
		age : { $ge : 15, $l : 21 }
	})
	console.log(result)
	/* Result:
		[
			{
				"name":"John",
				"age":20,
				"food": [
					"Burger","Sushi"
				]
			},
			{
				"name":"Alice",
				"age":15,
				"food": [
					"Orange","Fish"
				]
			}
		]
	*/

# db.update([collection/table], [filter], [newData])
- Find and retrieve data from database

Params | Required |  Type | Description
------------- | -------------
collection/table | True |  String | Target collection/table
filter | True | Object/Array  | Filter. Can be either **Object** or **Array**.
newData | True | Object/Array  | New data to overwrite. Can be either **Object** or **Array**.

Return
- Array

## Update All records

	/* Omitting async handles*/
	await db.update( 'users', {}, { food:[] } )
	let result = await db.find( 'users', {})
	console.log(result)
	/* Result:
		[
			{
				"name":"John",
				"age":20,
				"food": []
			},
			{
				"name":"Susan",
				"age":21,
				"food": []
			},
			{
				"name":"Alice",
				"age":15,
				"food": []
			}
		]
	*/

## Update One

	/* Omitting async handles*/
	/* Will automatically add any pre-undefined property */
	await db.update( 'users', { name:"John" }, [ { age: 25 }, { sports : "Basketball" } ] )
	let result = await db.find( 'users', { name:"John" } )
	console.log(result)
	/* Result:
		[
			{
				"name":"John",
				"age":25,
				"food": [],
				"sports":"Basketball"
			}
		]
	*/

- `filter` param works with ***Special Operators*** and `AND` too.

# db.delete([collection/table], [filter], [confirm])
- Find and retrieve data from database

Params | Required |  Type | Description
------------- | -------------
collection/table | True |  String | Target collection/table
filter | True | Object/Array  | Filter. Can be either **Object** or **Array**.
confirm | False | Object/Array  | Only needed when no filter is specified to protect accidentialy deletion of all data. Set it ***True*** to confirm the action.

Return
- None

## Delete one specific record

	/* Omitting async handles*/
	await db.delete( 'users', { name : "John" } )
	let result = await db.find( 'users', {})
	console.log(result)
	/* Result:
		[
			{
				"name":"Susan",
				"age":21,
				"food": []
			},
			{
				"name":"Alice",
				"age":15,
				"food": []
			}
		]
	*/

## Delete records with conditions

	/* Omitting async handles*/
	await db.delete( 'users', { age : { $ge : 15 } } )
	let result = await db.find( 'users', {})
	console.log(result)
	/* Result:
		[]
	*/

## Delete All

	/* Omitting async handles*/
	await db.delete( 'users', {}, true )
	

----

#  Special Operators
Operator  |  Descriptions
------------- | -------------
$ge | Greater and equals to
$g  | Greater than
$le  | Lesser and equals to
$l  | Lesser than

