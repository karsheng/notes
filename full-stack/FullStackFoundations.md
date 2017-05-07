# Full Stack Foundations

## Working With CRUD
1. Data driven app - CREATE, READ, UPDATE, DELETE (CRUD)
1. Object-Relational Mapping (ORM) - converting code from one form to another. E.g. Python object -> ORM -> SQL Statement and vice versa.
1. [SQLAlchemy](http://www.sqlalchemy.org/) is an example of ORM for Python.
1. Setting up a database with SQLAlchemy
	1. Configuration - imports necessary modules
	1. Class - represents data in Python
	1. Table - represents specific data in database
	1. Mapping - connects the columns of our table to the class that represents it
1. Configuration
```Python
import sys

from sqlalchemy import
Column, ForeignKey, Integer, String

from sqlalchemy.ext.declarative import
declarative_base

from sqlalchemy.orm import relationship

from sqlalchemy import create_engine

Base = declarative_base()

######insert at end of file ####

engine = create_engine(
'sqlite:///restaurantmenu.db')

Base.metadata.create_all(engine)
```
1. At terminal, you can do:
```
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from database_setup import Base, Restaurant, MenuItem 
engine = create_engine('sqlite:///restaurantmenu.db')
Base.metadata.bind = engine
DBSession = sessionmaker(bind=engine)
session = DBSession()
myFirstRestaurant = Restaurant(name = "Pizza Palace")
session.add(myFirstRestaurant)
session.commit()
session.query(Restaurant).all() # returns [<database_setup.Restaurant object at 0xb67701ec>]
cheesepizza = MenuItem(name = "Cheese Pizza", description = "Made with all natural ingredients and fresh mozzarella", course = "Entree", price = "$8.99", restaurant = myFirstRestaurant)
session.add(cheesepizza)
session.commit()
session.query(MenuItem).all() # returns [<database_setup.MenuItem object at 0xb678f64c>]
firstResult = session.query(Restaurant).first()
firstResult.name # returns u'Pizza Palace'
items = session.query(MenuItem).all()
for item in items:
...     print item.name
... 
Cheese Pizza
Veggie Burger
French Fries
Chicken Burger
Chocolate Cake
Sirloin Burger
Root Beer
Iced Tea
Grilled Cheese Sandwich
Veggie Burger
.......
UrbanVeggieBurger = session.query(MenuItem).filter_by(id = 8).one()
UrbanVeggieBurger.price = '$2.99' # this updates the price
session.add(UrbanVeggieBurger) # this updates the database
session.commit()
spinach = session.query(MenuItem).filter_by(name = 'Spinach Ice Cream').one()
session.delete(spinach) # this deletes item from database
session.commit()
```
## Making a Web Server
1. Client - computers that want info
1. Server - computers that has the info that can be shared with the client
1. Client initiates communication to request info, while server constantly stays listening to any clients to communicate with it and responds.
1. Protocols are like grammatical rule that we used to make sure all machines on the Internet are communicating in the same way.
1. Most computers adhere to:
	1. Transmission Control Protocol (TCP) - info broken into small packets sent between client and server. Counterpart of TCP is UDP (User Datagram Protocol), which is good for streaming content like music or video.
	1. Internet Protocol (IP) - Computer first look up the IP address of a website with a specific domain (say google.com) in a Domain Name Server (DNS), DNS gives the IP address to the client, then client initiates communication with server of that IP address.
		1. Multiple apps using the internet can run on one machine, OS use ports to designate channels of communication. E.g. :8080.
		1. Placing a colon after an IP address, with another number indicates that we want to communicate on a specific port on the device using that IP address.
		1. Ports usually range from 0 - 65,536. First 0 - 10,000 are often reserved by OS.
		1. Port 80 is the most common port for web servers.
		1. When client and server apps are on the same machine, we indicate this with the term localhost. Localhost has a special IP address of 127.0.0.1
	1. Hypertext Transfer Protocol (HTTP)
1. Client communicates with Server via HTTP method e.g. GET, POST. Server responds with a status code, together with any files that the Client requested if it was successful. E.g. 200: Successful GET, 301: Successful POST, 404: Not Found.
1. [Python HTTP Server Documentation](https://docs.python.org/2/library/basehttpserver.html)
1. `main()` instantiate our server and specify what port it will listen on.
1. `handler` code indicates what code to execute based on the type of HTTP request that is sent to the server.
1. [Port Forwarding](https://www.vagrantup.com/docs/networking/forwarded_ports.html) allows us to open pages in our browser from the webserver from our virtual machine as if they were being run locally. You can edit it in the `Vagrantfile`
1. `cgi` - common gateway interface.
1. Example simple server:
```Python
from BaseHTTPServer import BaseHTTPRequestHandler, HTTPServer
import cgi
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from database_setup import Base, Restaurant, MenuItem


engine = create_engine('sqlite:///restaurantmenu.db')
Base.metadata.bind = engine
DBSession = sessionmaker(bind=engine)
session = DBSession()


class webServerHandler(BaseHTTPRequestHandler):

    def do_GET(self):
        try:
            if self.path.endswith("/restaurant"):
            	self.send_response(200)
                self.send_header('Content-type', 'text/html')
                self.end_headers()
                restaurants = session.query(Restaurant).all()
                output = ""
                output += "<html><body>"
                output += '<a href="/restaurant/new">Make a new restaurant</a><br><br>'
                for restaurant in restaurants:
                	output += restaurant.name + "<br>"
                	output += '<a href="restaurant/%s/edit">Edit</a><br>' % restaurant.id
                	output += '<a href="restaurant/%s/delete">Delete</a><br>' % restaurant.id
                	output += '<br><br>'
                output += "</body></html>"
                self.wfile.write(output)
                return

            if self.path.endswith("/restaurant/new"):
                self.send_response(200)
                self.send_header('Content-type', 'text/html')
                self.end_headers()
                output = ""
                output += "<html><body>"
                output += "<h1>Make a New Restaurant</h1>"
                output += '''<form method='POST' enctype='multipart/form-data' action='/restaurant/new'>'''
                output += '''<input name="newRestaurantName" type="text" >'''
                output += '''<input type="submit" value="Submit">'''
                output += "</form>"
                output += "</body></html>"
                self.wfile.write(output)
                return

            if self.path.endswith("/edit"):
            	restaurantId = self.path.split("/")[2]
            	restaurant = session.query(Restaurant).filter_by(
            	    id=int(restaurantId)).one()
                self.send_response(200)
                self.send_header('Content-type', 'text/html')
                self.end_headers()
                output = ""
                output += "<html><body>"
                output += "<h1>" + restaurant.name + "</h1>"
                output += '''<form method='POST' enctype='multipart/form-data' action='restaurant/%s/edit'>''' % restaurantId
                output += '''<input name="editedRestaurantName" type="text" >'''
                output += '''<input type="submit" value="Submit">'''
                output += "</form>"
                output += "</body></html>"
                self.wfile.write(output)
                return

            if self.path.endswith("/delete"):
            	restaurantId = self.path.split("/")[2]
            	restaurant = session.query(Restaurant).filter_by(
            	    id=int(restaurantId)).one()
                self.send_response(200)
                self.send_header('Content-type', 'text/html')
                self.end_headers()
                output = ""
                output += "<html><body>"
                output += "<h1>Are you sure you wanna delete " + restaurant.name + "?</h1>"
                output += '''<form method='POST' enctype='multipart/form-data' action='restaurant/%s/delete'>''' % restaurantId
                output += '''<input type="submit" value="Delete">'''
                output += "</form>"
                output += "</body></html>"
                self.wfile.write(output)
                return

            if self.path.endswith("/hello"):
                self.send_response(200)
                self.send_header('Content-type', 'text/html')
                self.end_headers()
                output = ""
                output += "<html><body>"
                output += "<h1>Hello!</h1>"
                output += '''<form method='POST' enctype='multipart/form-data' action='/hello'><h2>What would you like me to say?</h2><input name="message" type="text" ><input type="submit" value="Submit"> </form>'''
                output += "</body></html>"
                self.wfile.write(output)
                print output
                return

            if self.path.endswith("/hola"):
                self.send_response(200)
                self.send_header('Content-type', 'text/html')
                self.end_headers()
                output = ""
                output += "<html><body>"
                output += "<h1>&#161 Hola !</h1>"
                output += '''<form method='POST' enctype='multipart/form-data' action='/hello'><h2>What would you like me to say?</h2><input name="message" type="text" ><input type="submit" value="Submit"> </form>'''
                output += "</body></html>"
                self.wfile.write(output)
                print output
                return

        except IOError:
            self.send_error(404, 'File Not Found: %s' % self.path)

    def do_POST(self):
        try:
        	if self.path.endswith("/delete"):
        		restaurantId = int(self.path.split("/")[2])
        		restaurant = session.query(Restaurant).filter_by(id = restaurantId).one()
        		session.delete(restaurant)
        		session.commit()
        		self.send_response(301)
            	self.send_header('Content-type', 'text/html')
            	self.send_header('Location', '/restaurant')
            	self.end_headers()
        		
        	if self.path.endswith("/edit"):
        		restaurantId = int(self.path.split("/")[2])
        		restaurant = session.query(Restaurant).filter_by(id = restaurantId).one()
        		ctype, pdict = cgi.parse_header(self.headers.getheader('content-type'))
        		if ctype == 'multipart/form-data':
        			fields = cgi.parse_multipart(self.rfile, pdict)
	                # gets content of specific field in a form and store them in
	                # an array
	                messagecontent = fields.get('editedRestaurantName')
	                restaurant.name = messagecontent[0]
	                session.add(restaurant)
	                session.commit()

            		self.send_response(301)
            		self.send_header('Content-type', 'text/html')
            		# this redirects the page
            		self.send_header('Location', '/restaurant')
            		self.end_headers()

        	if self.path.endswith("/restaurant/new"):
	            ctype, pdict = cgi.parse_header(
	                self.headers.getheader('content-type'))
	            # check if the data is from a form
	            if ctype == 'multipart/form-data':
	                # to collect all info of the fields in a form
	                fields = cgi.parse_multipart(self.rfile, pdict)
	                # gets content of specific field in a form and store them in
	                # an array
	                messagecontent = fields.get('newRestaurantName')
	                newRestaurant = Restaurant(name="%s" % messagecontent[0])
	                session.add(newRestaurant)
	                session.commit()

            		self.send_response(301)
            		self.send_header('Content-type', 'text/html')
            		# this redirects the page
            		self.send_header('Location', '/restaurant')
            		self.end_headers()       
        except:
            pass



def main():
    try:
        port = 8080
        server = HTTPServer(('', port), webServerHandler)
        print "Web Server running on port %s" % port
        server.serve_forever()
    except KeyboardInterrupt:
        print " ^C entered, stopping web server...."
        server.socket.close()

if __name__ == '__main__':
    main()

```

## Developing with Frameworks
1. Flask - minimal python framework
1. Minimal Flask app:
```Python
from flask import Flask
app = Flask(__name__) # a special variable called name gets defined for the app and all of the imports it uses.

@app.route('/') 

@app.route('/hello')
# These are decorator functions - wrap function inside the app.route function that Flask has created
# If either of these routes get sent from the browser, the function that we 
# define here (in this case HelloWorld()) gets executed.
def HelloWorld():
	return "Hello World"

# The app run by the Python interpreter gets a name variable set to
# __main__ whereas all the other imported Python files get a double underscore,
# set to the actual name of the Python file
if __name__ == '__main__': # this ensures that the script is only run from the python interpreter and not used as an imported module i.e. if execute from interpreter, do this, if imported, don't do this but you still have access to the code
	app.debug = True # This reloads the server each time there is code change, no need to reload yourself.
	app.run(host = '0.0.0.0', port = 5000)

```
1. More info on [decorator functions](http://simeonfranklin.com/blog/2012/jul/1/python-decorators-in-12-steps/)
1. By default, the server is only accessible from the host machine and not from any other computer. This is the default because in debugging mode, a user of the app can execute arbitrary Python code on your computer.
1. Using vagrant, we must make our server publicly available by changing the call of the run method to look like this `app.run(host = '0.0.0.0', port = 5000)`. This tells the web server on my vagrant machine to listen on all public IP addresses.
1. We don't have to write out response code anymore with Flask.
1. Specify variable in a URL: `path/<type: variable_name>/path/`
1. Render template by `render_template()`
1. Use `url_for` to get url for a specific handler.
1. Message flashing - prompts a message to the user immediately after a certain action has taken place, and then disappear the next time the page is requested - using the `flash` module.
1. Flashing works using a concept called sessions - a way a server can store information across multiple web pages, to create a more personalized user experience. Add a secret key to create sessions for our users.
1. Remember to import the relevant modules.
1. [Understanding `with`](http://effbot.org/zone/python-with-statement.htm)
1. Flask automatically looks for CSS, JS and media files in a folder called `static`. 
1. When an API is communicated over the internet, following the rules of HTTP, we call this a RESTful API.
1. JSON is the most popular way to send data.
1. Relevant lesson exercises in [fs-foundations](/Users/karshenglee/Dropbox/udacity/fullstack-dev/FSND-Virtual-Machine/vagrant/fs-foundations).