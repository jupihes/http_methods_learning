- What is the best way to combine http `get` and `post` and `cgi` to get details with post from one site and provide it as simple html via `cgi`. 
Please provide me with sample python code.

To combine HTTP GET and POST requests and generate a simple HTML response using Python and CGI, you can use the http.server module in Python's standard library. Here's a sample Python code that demonstrates this:

```python
from http.server import BaseHTTPRequestHandler, HTTPServer
import cgi

class MyHandler(BaseHTTPRequestHandler):
    
    def do_GET(self):
        # Set response code and headers
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        
        # Get the query parameters from the URL
        query_params = self.parse_query_params()
        
        # Perform any GET request processing here
        # For example, extract some details from another website using the provided query_params
        
        # Generate the HTML response
        response = "<html><body>"
        response += "<h1>Details for query: {0}</h1>".format(query_params)
        response += "<p>Details fetched from another website:</p>"
        response += "<ul>"
        # ... Add details to the response
        response += "</ul>"
        response += "</body></html>"
        
        # Send the HTML response
        self.wfile.write(response.encode())

    def do_POST(self):
        # Set response code and headers
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()
        
        # Get the form data from the POST request
        form = cgi.FieldStorage(
            fp=self.rfile,
            headers=self.headers,
            environ={'REQUEST_METHOD': 'POST'}
        )
        
        # Perform any POST request processing here
        # For example, extract and save the submitted form data
        
        # Generate the HTML response
        response = "<html><body>"
        response += "<h1>Form submitted successfully!</h1>"
        response += "<p>Submitted data:</p>"
        response += "<ul>"
        for field in form.keys():
            response += "<li>{0}: {1}</li>".format(field, form[field].value)
        response += "</ul>"
        response += "</body></html>"
        
        # Send the HTML response
        self.wfile.write(response.encode())
    
    def parse_query_params(self):
        """
        Parse and return the query parameters from the GET request URL.
        For example, if the URL is: /?param1=value1&param2=value2, this method will return:
        {'param1': 'value1', 'param2': 'value2'}
        """
        parsed_params = {}
        if self.path.startswith('/?'):
            query_params = self.path[2:].split('&')
            for param in query_params:
                key, value = param.split('=')
                parsed_params[key] = value
        return parsed_params


def run(server_class=HTTPServer, handler_class=MyHandler, port=8000):
    server_address = ('', port)
    httpd = server_class(server_address, handler_class)
    print('Starting server at http://localhost:{0}'.format(port))
    httpd.serve_forever()


run()
```

You can save the code in a Python file (e.g., server.py), then execute it from the command line. The script will start a server that listens on localhost:8000.

To test the GET request, open a web browser and visit http://localhost:8000/?param1=value1&param2=value2. You should see a simple HTML response with the details retrieved using the query parameters.

To test the POST request, you can use tools like curl or create a simple HTML form that submits to http://localhost:8000/. After submitting the form, the server will display the submitted form data in the HTML response.
