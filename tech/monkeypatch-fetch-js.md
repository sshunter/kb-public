# Monkeypatching fetch (Javascript)

**Note**: Untested code from ChatGPT, will be tested and expanded when I need
it.

Here's an example code snippet that shows how to monkey patch the fetch() method
to add a custom header to the request, and also to inspect the response for a
custom header:

```javascript
// Define the custom header name and value
const myHeaderName = 'X-My-Header';
const myHeaderValue = 'My Header Value';

// Define the monkey-patched fetch method
const originalFetch = fetch;
fetch = function(url, options) {
  // Add the custom header to the request
  options = options || {};
  options.headers = options.headers || new Headers();
  options.headers.append(myHeaderName, myHeaderValue);
  
  // Call the original fetch method with the updated options
  const fetchPromise = originalFetch.call(this, url, options);
  
  // Intercept the response and inspect it for the custom header
  const modifiedPromise = fetchPromise.then(function(response) {
    // Retrieve the value of the custom header, or null if it doesn't exist
    const myHeader = response.headers.get(myHeaderName);
    
    // Do something with the custom header value, if it exists
    if (myHeader !== null) {
      console.log('Custom header value:', myHeader);
    }
    
    // Return the original response to the caller
    return response;
  }).catch(function(error) {
    // Handle any errors that occur during the fetch
    console.error('Fetch error:', error);
    throw error;
  });
  
  // Return the modified fetch promise to the caller
  return modifiedPromise;
};

// Example usage
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(function(response) {
    if (response.ok) {
      console.log('Fetch success!');
      return response.json();
    } else {
      console.error('Fetch error:', response.statusText);
      throw new Error(response.statusText);
    }
  })
  .then(function(data) {
    console.log('JSON response:', data);
  })
  .catch(function(error) {
    console.error('Error:', error);
  });
```
  
In this example, we first define the custom header name and value that we want
to add to the request, and then we define the monkey-patched fetch() method.

The monkey-patched fetch() method first adds the custom header to the request by
updating the options object with the new header. It then calls the original
fetch() method with the updated options, and intercepts the response using the
then() method.

In the then() method, we retrieve the value of the custom header using the get()
method of the Headers object, and do something with it if it exists (in this
case, just log it to the console). We then return the original response to the
caller.

If an error occurs during the fetch, we catch it in the catch() method, log it
to the console, and re-throw it to ensure that any downstream error handling
logic can still catch it.

Finally, we demonstrate how to use the monkey-patched fetch() method by calling
it with a URL and handling the response using the then() method. If the response
is successful (i.e., response.ok is true), we log the JSON data to the console.
If an error occurs, we log it to the console and re-throw it to ensure that any
downstream error handling logic can still catch it.
