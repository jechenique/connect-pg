# Connect PostgreSQL

Connect-pg is a middleware session storage for the connect 
framework using PostgreSQL.  Why?  Because sometimes you need a 
relational database handling your data.  

## Requirements

* **Production**
	* *[connect](https://github.com/senchalabs/connect) 1.5.0 or later* The HTTP server framework used by Express.
	* *[pg](https://github.com/brianc/node-postgres) 0.50 or later* The node.js client for PostgreSQL.  
	* *[PostgreSQL](http://www.postgresql.org) 8.4 or later* The database.
* **Development**
	* *[jasmine-node](https://github.com/mhevery/jasmine-node)* The BDD style testing framework for JavaScript.  
	* *[pgtap](http://pgtap.org)* TAP style testing framework for PostgreSQL databases.  
	
## Installation 

Installation is done in two steps.  The first is to install the JavaScript library, 
and the second is to add the tables to the PostgreSQL database.  

1. **Install the JavaScript library**

	*Standard Method:* npm install connect-pg (eventually)
	
	*Manual Method:* [Download](https://github.com/jebas/connect-pg) the files to your
	server.  The only file your script needs access to is connect-pg.js found in the lib
	directory.  
	
2. **Add the tables to your database**

	In the lib directory, there is a file called session_install.sql.  Assuming that you
	have already created a database, and that you have access to a user that has been 
	granted the appropriate permissions (See the [PostgreSQL](http://www.postgresql.org/docs)
	for instructions), all you need to do is execute session_install.sql inside the database.
	
	`psql -d pgstore -U postgres -f session_install.sql`
	
	If you are planning to use the same role/user in your JavaScript application, you are 
	done with the installation.  However, all of the functions were created with security
	definer.  This will allow you to create another role that only has execute permissions 
	for the functions.  
	
	
	`grant execute on function web.set_session_data(text, text, timestamp with time zone) to scriptrole;`
	
	`grant execute on function web.get_session_data(text) to scriptrole;`
	
	`grant execute on function web.destroy_session(text) to scriptrole;`
	
	`grant execute on function web.clear_sessions() to scriptrole;`
	
	`grant execute on function web.count_sessions() to scriptrole;`
	
	`grant execute on function web.all_session_ids() to scriptrole;`
	
## Usage

Using connect-pg can be done in three easy steps.  

1. After the database has been created, the next step is to let your application is going to 
use connect-pg.  

	`var PGStore = require('connect-pg');`

2. Next establish your database connection string.

	`var connectStr = "tcp://thetester:password@localhost/pgstore";`
	
	`var storeOptions = {'pgConnect': connectStr};`
	
3. Inform the session manager to use connect-pg.  

	* **In connect:**
	
		`connect.session({ store: new PGStore(storeOptions), secret: 'keyboard cat'});`
		
	* **In Express:**
	
		`app.use(express.session({store: new PGStore(storeOptions), secret: 'keyboard cat'}));`
		
## Development 

connect-pg use two testing systems: one for the database, and one for the JavaScript.  

In a traditional model-controller-view (MCV) setup, the database would be considered 
the model and all access to the database is controlled through functions inside the 
database.  pgtap is used to test these functions.  Installation of pgtap is described on
their website.  

The rest of connect-pg could be considered the controller, and it was written in 
JavaScript.  `jasmine-node spec` is all that is needed to run these tests.  

There is no view to setup.  

## LICENSE

This software is using the [MIT](./connect-pg/blob/master/LICENSE) to match the connect license.