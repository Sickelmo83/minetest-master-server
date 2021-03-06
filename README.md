Minetest server list
====================

Setting up the webpage
----------------------

You will have to install node.js, doT.js and their dependencies to compile
the server list webpage template.

First install node.js, e.g.:

	# apt-get install nodejs
	# # OR:
	# pacman -S nodejs
	# # OR:
	# emerge nodejs

Then install doT.js and its dependencies:

	$ cd ~
	$ npm install dot commander mkdirp

And finally compile the template:

	$ cd static
	$ ~/node_modules/dot/bin/dot-packer -s .

You can now serve the webpage by copying the files in static/ to your web root, or by [starting the master server](#setting-up-the-server).


Embedding the server list in a page
-----------------------------------

	<head>
		...
		<script>
			var master = {
				root: 'http://servers.minetest.net/',
				limit: 10,
				clients_min: 1,
				no_flags: 1,
				no_ping: 1,
				no_uptime: 1
			};
		</script>
		...
	</head>
	<body>
		...
		<div id="server_list"></div>
		...
	</body>
	<script src="list.js"></script>


Setting up the server
---------------------

  1. Install Python 3 and pip:
  
	pacman -S python python-pip
	# OR:
	apt-get install python3 python3-pip

  2. Install required Python packages:

	# You might have to use pip3 if your system defaults to Python 2
	pip install -r requirements.txt

  3. If using in production, install uwsgi and it's python plugin:

	pacman -S uwsgi uwsgi-plugin-python
	# OR:
	apt-get install uwsgi uwsgi-plugin-python
	# OR:
	pip install uwsgi

  4. Configure the server by adding options to `config.py`.
       See `config-example.py` for defaults.

  5. Start the server:

	$ ./server.py
	$ # Or for production:
	$ uwsgi -s /tmp/minetest-master.sock --plugin python -w server:app --enable-threads
	$ # Then configure according to http://flask.pocoo.org/docs/deploying/uwsgi/

  7. (optional) Configure the proxy server, if any.  You should make the server
	load static files directly from the static directory.  Also, `/list`
	should be served from `list.json`.  Example for nginx:

	root /path/to/server/static;
	rewrite ^/list$ /list.json;
	try_files $uri @uwsgi;
	location @uwsgi {
		uwsgi_pass ...;
	}

License
-------

The Minetest master server is licensed under the GNU Lesser General Public
License version 2.1 or later (LGPLv2.1+).  A LICENSE.txt file should have been
supplied with your copy of this software containing a copy of the license.

