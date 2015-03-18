# Error-handling
how to handle errors properly in Express

"full stack error handling"
- frontend
- backend



Error Handling in Node.js by Jamund Ferguson: https://www.youtube.com/watch?v=p-2fzgfk9AA&t=395

Best Practices

1) If you parse a JSON, try catch it. Otherwise, DO NOT USE TRY/CATCH elsewhere in Javascript

var user; 
try { 
  user = JSON.parse(data); 
} catch(e) { 
  // handle the error 
}
return user; 

2) Callback Pattern. 

Always handle the error object

fs.readFile('a.txt', function(err, data) { 
  if (err) { 
    // handle the error 
    return; 
  } 
  
  doSomethingElseWith(data); 
}); 

3) Create your own Custom Error Objects 

function MountainError(message) { 
  Error.captureStackTrack(this); 
  this.message = message; 
  this.name = "MountainError"; 
} 

MountainError.prototype = Object.create(Error.prototype); 
}

4) Conventions to follow
- always deal in Errorobjects
- next(err) and then immediately return 
- process.on('uncaughtException') must always process.exit(); 
- use a helper function for sending errors to client 
- log every error 
- 



Code Example: 

function handleError(err, req, res) {

  logError(err); 
  
  var message = err ? err.message : "Internal Server Error"; 
  
  res.json({ 
    error: {message: message}
  }); 
  
  function logError(error) { 
    console.log({ 
      message: error.message, 
      stack: error.stack
    });
  }
}


app.get('/mountains/:id', function(req, res) { 
  db.get(req.params.id, function(err, use) { 
    if (err) { 
      return handleError(err, req, res);
    }
    
    res.json(user); 
  }); 
}); 


https://www.youtube.com/watch?v=qkYWb1bC_ws

3 ways to handle errors: 

1) try/catch <-- doesn't work with async code
2) callbacks <-- can become callback hell of try/catches 
3) promises 

Promises

function("value", function(err, results) { ... }); 

function("value").then( 
  function(results) { ... }, 
  function(err) { ... }

----

function updateDependencies(packageName) { 
  return findPackage(packageName)
    .then(function(content) { 
      return JSON.parse(content); 
    })
    .then(function(package) { 
      return findDependencies(package); 
    })
    .then(function(deps) { 
      return processDependencies(deps);
    }); 
  }


usage: 

updateDependencies("npm") 
  .then(function() { 
    console.log("Done!"); 
  })
  .error(function(err) { 
    console.error(err.stack); 
  }); 
  


shortened code: 

function updateDependencies(packageName) { 
  return findPackage(packageName)
    .then(JSON.parse)
    .then(findDependencies)
    .then(processDependencies);
  }
  
  
  https://www.npmjs.com/package/request-promise
  
  
  // As a Request user you would write: 
var request = require('request');
 
request('http://google.com', function (err, response, body) {
    if (err) {
        handleError({ error: err, response: response, ... });
    } else if (!(/^2/.test('' + response.statusCode))) { // Status Codes other than 2xx 
        handleError({ error: body, response: response, ... });
    } else {
        process(body);
    }
});
 
// As a Request-Promise user you can now write the equivalent code: 
var rp = require('request-promise');
 
rp('http://google.com')
    .then(process, handleError);
  
