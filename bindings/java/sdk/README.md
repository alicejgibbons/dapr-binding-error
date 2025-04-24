# Dapr Bindings HTTP Binding Error Repro

On REST client when you run the dapr sidecar (only) and then hit the following endpoint with the respective body, you get a status code 400 with an Error code in the response.

![http error](java/sdk/http-error.png)

Endpoint: `http://localhost:3500/v1.0/bindings/http`
Body:

```
{
  "operation": "post",
  "data": "content (default is JSON)",
  "metadata": {
    "path": "/things",
    "Content-Type": "application/json; charset=utf-8"
  }
}
```

Response: 
```
{
    "errorCode": "ERR_INVOKE_OUTPUT_BINDING",
    "message": "error invoking output binding http: received status code 400"
}
```

Then by running the sample app (steps below), you can see that the output of the application doesn't contain the 400 response status code from the response when inspecting the DaprException object:

```
== APP == 2025-04-24T20:19:32.010+01:00  INFO 67463 --- [nio-8080-exec-3] c.s.c.BatchProcessingServiceController   : Processing batch..
== APP == Dapr exception's error code: (DaprException.getErrorCode()): INTERNAL
== APP == Dapr exception's message: (DaprError.getMessage()) INTERNAL: error invoking output binding http: received status code 400
== APP == Dapr exception's error details (DaprException.getErrorDetails())): ERR_INVOKE_OUTPUT_BINDING
== APP == Dapr exception's getHttpStatusCode: (DaprException.getHttpStatusCode()): 0
== APP == Error's payload: (DaprException.getPayload()): [B@df87a64
== APP == Error's payload size: (DaprException.getPayload().length): 146
```

Specifically this line "== APP == Dapr exception's getHttpStatusCode: (DaprException.getHttpStatusCode()): 0"

### Run Java service with Dapr

2. Open a new terminal window, change directories to `./batch` in the quickstart directory and run: 

<!-- STEP
name: Install Java dependencies
-->

```bash
cd bindings/java/sdk/batch/ 
mvn clean install
```

<!-- END_STEP -->
3. Run the java service app with Dapr: 

<!-- STEP
name: Run batch-sdk service
working_dir: ./batch
expected_stdout_lines:
  - 'insert into orders (orderid, customer, price) values (1, ''John Smith'', 100.32)'
  - 'insert into orders (orderid, customer, price) values (2, ''Jane Bond'', 15.4)'
  - 'insert into orders (orderid, customer, price) values (3, ''Tony James'', 35.56)'
  - 'Finished processing batch'
expected_stderr_lines:
output_match_mode: substring
sleep: 11
timeout_seconds: 30
-->
    
```bash
dapr run --app-id batch-sdk --app-port 8080 --resources-path ../../../components -- java -jar target/BatchProcessingService-0.0.1-SNAPSHOT.jar
```

<!-- END_STEP -->
