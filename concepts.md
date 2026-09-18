# Concepts, explained simply

Notes from building real projects, each entry in plain language,
the way I'd explain it to a student.

## 502 vs 504 vs 500 — whose failure is it?


When your API calls another API, things fail and your status code must say whose fault it was.. Even though there can be a lot of different reasons for the error, it can all look like the fault of your code if the API calls are not handled properly.
That's why we handle the exceptions, while calling to get the response  from an external API. 

- Use httpx.HTTPError (status code - 502), if the external API didn't respond properly.
- Use httpx.TimeoutException (status code- 504), if the API took longer to respond, than  the amount of timeout you have set. Seeing 502/504 they should retry later because the problem is upstream.
- Status code 500, if it's your own code's fault. A client seeing 500 should report a bug to you.