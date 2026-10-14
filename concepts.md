# Concepts, explained simply

Notes from building real projects, each entry in plain language,
the way I'd explain it to a student.

## 502 vs 504 vs 500 — whose failure is it?


When your API calls another API, things fail and your status code must say whose fault it was.. Even though there can be a lot of different reasons for the error, it can all look like the fault of your code if the API calls are not handled properly.
That's why we handle the exceptions, while calling to get the response  from an external API. 

- Use httpx.HTTPError (status code - 502), if the external API didn't respond properly.
- Use httpx.TimeoutException (status code- 504), if the API took longer to respond, than  the amount of timeout you have set. Seeing 502/504 they should retry later because the problem is upstream.
- Status code 500, if it's your own code's fault. A client seeing 500 should report a bug to you.


## What my TTL cache can and can't do

I built an in-process TTL cache keyed by URL. But, I understand the drawbacks of this method. The cache only lives as long as the process is running, as soon as the process stops the cache loses memory and that is a big drawback because it can create a situation of cache stampede upon restarting the process.
Also cache can't be shared by different processes, that is the problem that Redis solves. Errors are never cached in my project — deliberately: failures are usually temporary, and a cached error would keep serving "broken" after the upstream recovered. The narrow exception in the wild is "negative caching" — remembering a failure for just a few seconds so a crowd of clients doesn't keep hammering a service that's already down


## An LLM API is just an API

I recently got to use the Claude API and I discovered that an LLM API is just like a normal API.
I made a post end-point where the user inputs their query, I take that as request body and give it to the claude API. The API returns a message with the result in it, just like what happens in a normal API.
Additionally, Claude API also shows the usage of tokens in the json response. It shows how many tokens that we have paid for, have been spent in the input, output and other things. We extract the "content" from the json response of the API. Content is a list of typed blocks, not plain text — my first call returned only a 'thinking' block with zero words, so I filter for text blocks.
I learned that this API is stateless, which means it can't remember things we told it in the past conversations. So to have a conversation, I must resend the whole history with every call.
