# Intro To Back End
## Forms
1. GET
	1. Parameters in URL
	1. Used for fetching docs
	1. Maximum URL length - limited data to be sent to server
	1. OK to cache
	1. Shouldn't change the server
1. POST
	1. Parameters in body
	1. Used for updating data
	1. No max length
	1. Not OK to cache
	1. Ok to change the server
1. Basecamp used links (GET request to do delete to do list), Google Web Accelerator hits those links and delete them. Important to use POST to alter server data.
1. Give radio buttons same `name` to make them behave like it would - switch off when another is selected. Use `value` parameter for differentiation. In this case the parameter submitted (say `<input name="q" type="radio" value="two">`) will be `q=two`.
1. By default, dropdown overwrites submitted value specified, if it's not specified then it will take the option value.
1. Your server can receive junk - make sure you always validate your parameters on the server side.
1. Dict example
![Dictionary Example](../img/dict-example.png)
1. Replacing strings:
```python
	t1 = "I think %s is a perfectly normal thing to do in public."
	t2 = "I think %s and %s is fun"

	def sub1(s):
		return t1 % s

	def sub2(s):
		return t1.replace("%s", s)

	#replace multiple string variables
	def sub3(s1, s2)
		return t2 % (s1, s2)

	#with repeating variables
	given_string2 = "I'm %(nickname)s. My real name is %(name)s, but my friends call me %(nickname)s."
	def sub_m(name, nickname):
    	return given_string2 % {"nickname" : nickname, "name" : name}
	
	print sub_m("Mike", "Goose") 	
```
1. import cgi module for escape functions
1. Advantage of redirecting:
	1. So that reloading page doesn't resubmit the form
	1. Distinct pages for forms and success pages

## Template
1. A template library is a library to build complicated strings
1. [Jinja2](http://jinja.pocoo.org/docs/dev/) is built into Google App Engine. 
1. `{{var}}` use to substitute variable in HTML template.
1. Always automatically escape variables when possible - although not every template language allows you to do so. Jinja does.
1. Minimize code in templates
1. Minimize html in code

## Databases
1. A program that stores and retrieves large amount of structured data, a machine running that program, and a group of machines working together to store/retrieve data.
1. [`namedTuple`](https://docs.python.org/2/library/collections.html#collections.namedtuple)
1. [`lambda`](https://docs.python.org/2/tutorial/controlflow.html#lambda-expressions) and [`sort`](https://wiki.python.org/moin/HowTo/Sorting)
1. Relational Databases (often uses SQL to manipulate data):
	1. PostgresSQL(reddit)
	1. MySQL(Facebook)
	1. SQLite(a light-weight simple database)
	1. Oracle
1. Other databases: 
	1. Google App Engine's Datastore
	1. Dynamo by Amazon
	1. NoSQL - Mongo, Couch
1. SQL (Structured Query Language) - language for expressing queries. Invented in 1970s, some things aren't exactly relevant.
1. E.g. `SELECT * FROM links WHERE id = 5`
1. When you query, a cursor is created which is just a position in the database.
```Python
# after importing SQLite and stuff
def query():
	c = db.execute("select * from links")
	for link_tuple in cursor:
		link = link(*link_tuple) # Python syntax for passing in all of the parameters in this tuple as arguments to this function
		print link.vote
query()
```
```Python
# after importing SQLite and stuff
def query():
	c = db.execute("select * from links")
	results = c.fetchall()
	return results
print query()
```

```Python
from collections import namedtuple
import sqlite3

# make a basic Link class
Link = namedtuple('Link', ['id', 'submitter_id', 'submitted_time', 'votes',
                           'title', 'url'])

# list of Links to work with
links = [
    Link(0, 60398, 1334014208.0, 109,
         "C overtakes Java as the No. 1 programming language in the TIOBE index.",
         "http://pixelstech.net/article/index.php?id=1333969280"),
    Link(1, 60254, 1333962645.0, 891,
         "This explains why technical books are all ridiculously thick and overpriced",
         "http://prog21.dadgum.com/65.html"),
    Link(23, 62945, 1333894106.0, 351,
         "Learn Haskell Fast and Hard",
         "http://yannesposito.com/Scratch/en/blog/Haskell-the-Hard-Way/"),
    Link(2, 6084, 1333996166.0, 81,
         "Announcing Yesod 1.0- a robust, developer friendly, high performance web framework for Haskell",
         "http://www.yesodweb.com/blog/2012/04/announcing-yesod-1-0"),
    Link(3, 30305, 1333968061.0, 270,
         "TIL about the Lisp Curse",
         "http://www.winestockwebdesign.com/Essays/Lisp_Curse.html"),
    Link(4, 59008, 1334016506.0, 19,
         "The Downfall of Imperative Programming. Functional Programming and the Multicore Revolution",
         "http://fpcomplete.com/the-downfall-of-imperative-programming/"),
    Link(5, 8712, 1333993676.0, 26,
         "Open Source - Twitter Stock Market Game - ",
         "http://www.twitstreet.com/"),
    Link(6, 48626, 1333975127.0, 63,
         "First look: Qt 5 makes JavaScript a first-class citizen for app development",
         "http://arstechnica.com/business/news/2012/04/an-in-depth-look-at-qt-5-making-javascript-a-first-class-citizen-for-native-cross-platform-developme.ars"),
    Link(7, 30172, 1334017294.0, 5,
         "Benchmark of Dictionary Structures", "http://lh3lh3.users.sourceforge.net/udb.shtml"),
    Link(8, 678, 1334014446.0, 7,
         "If It's Not on Prod, It Doesn't Count: The Value of Frequent Releases",
         "http://bits.shutterstock.com/?p=165"),
    Link(9, 29168, 1334006443.0, 18,
         "Language proposal: dave",
         "http://davelang.github.com/"),
    Link(17, 48626, 1334020271.0, 1,
         "LispNYC and EmacsNYC meetup Tuesday Night: Large Scale Development with Elisp ",
         "http://www.meetup.com/LispNYC/events/47373722/"),
    Link(101, 62443, 1334018620.0, 4,
         "research!rsc: Zip Files All The Way Down",
         "http://research.swtch.com/zip"),
    Link(12, 10262, 1334018169.0, 5,
         "The Tyranny of the Diff",
         "http://michaelfeathers.typepad.com/michael_feathers_blog/2012/04/the-tyranny-of-the-diff.html"),
    Link(13, 20831, 1333996529.0, 14,
         "Understanding NIO.2 File Channels in Java 7",
         "http://java.dzone.com/articles/understanding-nio2-file"),
    Link(15, 62443, 1333900877.0, 1244,
         "Why vector icons don't work",
         "http://www.pushing-pixels.org/2011/11/04/about-those-vector-icons.html"),
    Link(14, 30650, 1334013659.0, 3,
         "Python - Getting Data Into Graphite - Code Examples",
         "http://coreygoldberg.blogspot.com/2012/04/python-getting-data-into-graphite-code.html"),
    Link(16, 15330, 1333985877.0, 9,
         "Mozilla: The Web as the Platform and The Kilimanjaro Event",
         "https://groups.google.com/forum/?fromgroups#!topic/mozilla.dev.planning/Y9v46wFeejA"),
    Link(18, 62443, 1333939389.0, 104,
         "github is making me feel stupid(er)",
         "http://www.serpentine.com/blog/2012/04/08/github-is-making-me-feel-stupider/"),
    Link(19, 6937, 1333949857.0, 39,
         "BitC Retrospective: The Issues with Type Classes",
         "http://www.bitc-lang.org/pipermail/bitc-dev/2012-April/003315.html"),
    Link(20, 51067, 1333974585.0, 14,
         "Object Oriented C: Class-like Structures",
         "http://cecilsunkure.blogspot.com/2012/04/object-oriented-c-class-like-structures.html"),
    Link(10, 23944, 1333943632.0, 188,
         "The LOVE game framework version 0.8.0 has been released - with GLSL shader support!",
         "https://love2d.org/forums/viewtopic.php?f=3&amp;t=8750"),
    Link(22, 39191, 1334005674.0, 11,
         "An open letter to language designers: Please kill your sacred cows. (megarant)",
         "http://joshondesign.com/2012/03/09/open-letter-language-designers"),
    Link(21, 3777, 1333996565.0, 2,
         "Developers guide to Garage48 hackatron",
         "http://martingryner.com/developers-guide-to-garage48-hackatron/"),
    Link(24, 48626, 1333934004.0, 17,
         "An R programmer looks at Julia",
         "http://www.r-bloggers.com/an-r-programmer-looks-at-julia/")]

# links is a list of Link objects. Links have a handful of properties. For
# example, a Link's number of votes can be accessed by link.votes if "link" is a
# Link.

# make and populate a table
db = sqlite3.connect(':memory:')
db.execute('create table links ' +
          '(id integer, submitter_id integer, submitted_time integer, ' +
          'votes integer, title text, url text)')
for l in links:
    db.execute('insert into links values (?, ?, ?, ?, ?, ?)', l)

# db is an in-memory sqlite database that can respond to sql queries using the
# execute() function.
#
# For example. If you run
#
# c = db.execute("select * from links")
#
# c will be a "cursor" to the results of that query. You can use the fetchmany()
# function on the cursor to convert that cursor into a list of results. These
# results won't be Links; they'll be tuples, but they can be passed turned into
# a Link.
#
# For example, to print all the votes for all of the links, do this:
#
# c = db.execute("select * from links")
# for link_tuple in c:
#     link = Link(*link_tuple)
#     print link.votes
#
# QUIZ - make the function query() return the number of votes the link with ID = 2 has
def query():
    c = db.execute("select * from links where id = 2")

    link = Link(*c.fetchone())
    return link.votes

print query()

def query():
    c = db.execute("select * from links where submitter_id = 62443 order by submitted_time ASC ")
    results = []
    for link_tuple in c:
        link = Link(*link_tuple)
        results.append(link.id)
    return results
print query()
```
1. Should avoid joins if possible1
1. Indexes - makes looking up things faster.
1. User `get()` to retreive values in hash table (dictionary) that may or may not exist in order to avoid error that you would get by using `dict[key]`
1. `dict[-1]` gets the last element in the dictionary
1. Indices increase the speed of database reads but decreases the speed of database inserts becauase we have to update all of our indices when there is a new insert, which takes time.
1. Hashtables is not sorted but faster lookup as it has constant time lookup.
1. Tress are sorted but lookups are slower which are function of log n where n is the number of elements in a tree.
1. Some databases may let you choose which structure to use.
1. Shard the database is an appropriate technique for growing a database that won't fit on one machine.
1. ACID - 
	1. Atomicity - all parts of a transaction succeed or fail together - say when updating two tables, both have to be successful or they will fail together.
	1. Consistency - database will always be consistent
	1. Isolation - no transaction can interfere with another - e.g. two votes (up and down) coming in the same time, the upvote shouldn't affect the computation of the downvote. Sometimes this is achieved by locking - if two transactions affect the same row, only one can go at a time.
	1. Durability - once a transaction is committed it won't be lost.
1. The key takeaway is the it's impossible for a database to achieve complete ACID, there's always some tradeoff.
1. In Google App Engine Datastore:
	1. Entities are basically tables but:
		1. columns are not fixed, 
		1. all have an ID, 
		1. have parents and ancestors
	1. GQL (rather than SQL)
		1. all queries begin with SELECT * (no way to select individual columns), hence can't do joins
		1. all queries must be indexed, which the Google App Engine will build
	1. Datastore is sharded and replicated - won't have to think about scaling too much
	1. Queries will be quick (because they have to be simple)
	1. Might have to think about consistency - because things are sharded and your data changes and updates take time to propagate through the system, we have to acknowledge the fact that we're in a big database system and things may not always be consistent in all cases. It's easy to work around but its something you have to do consciously.
	1. String vs. Text - string must be < 500 characters and Text can be 500 and more. Text can't be indexed i.e. can't be sorted.
## User Accounts & Security
1. Cookies - small(< 4kb, <100bytes in practice) piece of data stored in the browser for a website. In the form of name = value  user.id = 12345.
1. Browser send requests to web server and web server might send back in it's response, some cookie data in the form of HTTP header. Browser stores the cookies associated with this website. Everytime browser makes request to server it will send its cookies back to the server - this is how server knows you're logged in. 
1. Browser Limit:
	1. You can store about 20 cookies per website (this is a browser limitation)
	1. A cookie is less than 4 kb
	1. Has to be associated with a domain
1. Cookies is good for:
	1. Storing login info
	1. Storing small amounts of data to avoid hitting a db
	1. Tracking you for ads - tracking you for different ad providers.
1. HTTP response:
	1. set-cookie user_id = 12345
1. Server can send as many cookies as it wants. Its up to the browser to decide if they want to store them, about 20 is the max.
1. In future request the browser sends its own header. To send multiple headers: `cookie: user_id=12345;last-seen:Dec22017`
1. Don't put semicolon in your cookie, if you really want to, encode it.
1. [Cookies Example](https://www.youtube.com/watch?v=jxOEKSu0vws)
1. On a mac, to see HTTP headers: `curl -I www.google.com`
1. [List of HTTP header fields](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)
1. Set-Cookie:name=steve;Domain=ide.udacity.com;
	1. Valid setter: ide.udacity.com and other.ide.udacity.com
	1. Valid receivers: ide.udacity.com and other.ide.udacity.com
1. [Cookie domains](https://www.youtube.com/watch?v=xgW0XjOeusY)
1. [Ad Networks](https://www.youtube.com/watch?v=qMFRRoh6vV8)
1. Session cookie disappears when you close the browser.
1. When you checked remember me you basically set the cookie with expiration.
1. [Cookie example](https://www.youtube.com/watch?v=MdhLZl72vpk)
1. To edit cookie, at the console write e.g. `document.cookie="visits=10001`
1. Hash is a function to change X to Y H(x) => y. x is data and y is fixed length bit string (32 - 256 bits) This technique is used to verify legitimacy of our data.
1. Its difficult to generate a specific y. It's infeasible to find x for a given y (one way). You can't modify x without modifying y.
1. Collision - two thing hash to the same value.
1.  Popular Hash Algorithms:
	1. crc32 - designed for checksums, fast. Easy to find collision.
	1. md5 - fast and no so secure anymore.
	1. sha1 - secure-ish
	1. sha256 - good but slowest among these four
1. [Hashing in Python](https://www.youtube.com/watch?v=EHLfXD2YVMw)
1. hashing cookies
	1. `Set-Cookie:visits=5[hash]` to browser
	1. from browser - 5, abc123
1. Note that you need to use a `|` here instead of a `,` when making your secure values, because of an issue Google App Engine has with `,` in cookies.
1. Cookie hashing - has a secret string plus the value into a hash.
	1. H(SECRET + 1) = HASH
	1. HMAC(Hash-based Message Authentication Code) - a function built into Python which is used to combine a key with a value to create a hash.
1. Hashed password still doesn't mean its safe.
1. Rainbow table is a precomputed table for reversing cryptographic hash functions, usually for cracking password hashes. 
1. Salt - hash = H(pw + salt), salt is just some random characters. Use third party libraries carefully and don't implement it yourself.
1. Validating Salts:
```Python
import random
import string
import hashlib

def make_salt():
    return ''.join(random.choice(string.letters) for x in xrange(5))

# Implement the function valid_pw() that returns True if a user's password 
# matches its hash. You will need to modify make_pw_hash.

def make_pw_hash(name, pw, salt = None):
    if not salt:
        salt = make_salt()
    h = hashlib.sha256(name + pw + salt).hexdigest()
    return '%s,%s' % (h, salt)

def valid_pw(name, pw, h):
    ###Your code here
    salt = h.split(",")[1]
    
    return h == make_pw_hash(name, pw, salt)

h = make_pw_hash('spez', 'hunter2')
print valid_pw('spez', 'hunter2', h)

```
1. Use `bcrypt` instead of `sha256`.
1. Use HTTPS to make sure password is encrypted the whole way.
1. 