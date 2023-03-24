[#](#.md) Monkeypatching fetch (Typescript)

**Note**: Untested code from ChatGPT, will be tested and expanded when I need
it.

An example code snippet that shows how to monkey patch the fetch() method to add
a custom header to the request, and also to inspect the response for a custom
header:

```typescript
const myHeaderName = 'X-My-Header';
const myHeaderValue = 'My Header Value';

type Fetch = typeof fetch;
type Options = Parameters<Fetch>[1];

const originalFetch = window.fetch;

window.fetch = async function(url: RequestInfo, options?: Options): Promise<Response> {
  options = options ?? {};
  options.headers = options.headers ?? new Headers();
  options.headers.append(myHeaderName, myHeaderValue);
  
  try {
    const response = await originalFetch.call(this, url, options);
    
    const myHeader = response.headers.get(myHeaderName);
    if (myHeader !== null) {
      console.log('Custom header value:', myHeader);
    }
    
    return response;
  } catch (error) {
    console.error('Fetch error:', error);
    throw error;
  }
};

// Example usage
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(async (response) => {
    if (response.ok) {
      console.log('Fetch success!');
      const data = await response.json();
      console.log('JSON response:', data);
    } else {
      console.error('Fetch error:', response.statusText);
      throw new Error(response.statusText);
    }
  })
  .catch(function(error) {
    console.error('Error:', error);
  });
```
We first define the myHeaderName and myHeaderValue constants. We then define the
types Fetch and Options to make the code more type-safe.

The originalFetch constant is defined as a reference to the original fetch()
method, which is stored in the window.fetch property.

The monkey-patched fetch() method is defined as an async function that takes a
url parameter of type RequestInfo, which can be either a string URL or a Request
object. The options parameter is defined as an optional parameter of type
Options, which is a tuple containing the RequestInit options for the fetch.

Inside the monkey-patched fetch() method, we first check if the options
parameter is null or undefined, and if so, set it to an empty object. We then
check if the headers property of the options object is null or undefined, and if
so, set it to a new Headers object. We then append the custom header to the
headers object.

We then call the originalFetch() method using the call() method, passing in this
as the first argument to maintain the correct this context. We await the
response to get the full Response object.

Next, we retrieve the value of the custom header using the get() method of the
Headers object, and log it to the console if it exists. We then return the
Response object.

If an error occurs during the fetch, we catch it using a try...catch block, log
it to the console, and re-throw it to ensure that any downstream error handling
logic can still catch it.

Finally, we demonstrate how to use the monkey-patched fetch() method by calling
it with a URL and handling the response using the then() method. If the response
is successful (i.e., response.ok is true), we log the JSON data to the console.
If an error occurs, we log it to the console and re-throw it to ensure that any
downstream error handling logic can still catch it.
