# **Circuit Breakers**

## **What ?**

A circuit breaker is a design pattern used to prevent cascading failures and manage service availability. If a service call repeatedly fails, the circuit breaker "trips" and prevents further attempts, allowing the system to recover gracefully. This pattern mimics the behavior of electrical circuit breakers.

## **Why is it Needed ?**

- **Fault tolerance**: Preventing cascading failures in microservices.
- **Graceful degradation**: Avoiding overloading failing services.
- **Latency reduction**: Avoid waiting indefinitely on unresponsive services.
- **Network management**: Protects against service exhaustion from too many retries.

!!! abstract "Real-world scenario"
    If Service A depends on Service B but Service B becomes unavailable, Service A will receive failures continuously. A circuit breaker prevents Service A from overloading itself and Service B by failing fast.


## **Types of Circuit Breakers**

There are multiple models of circuit breakers to choose from depending on use case:

**Count-based Circuit Breaker**
    - Trips if a predefined number of failures occur, eg: If there are 3 consecutive failed requests, the breaker opens.

**Time-based Circuit Breaker**
    - Monitors failures within a window of time and trips if the failure threshold is met, eg: If 5 requests out of 10 fail within 1 minute, it opens.

**Sliding Window Circuit Breaker**
    - A rolling window of requests over time determines if the circuit trips, Useful when failure patterns are sporadic.


## **How Does it Work?**

The basic mechanics of a circuit breaker involve three states:

**Closed State** 

- All requests are passed through.
- If failures exceed the threshold, it trips to the Open state.

**Open State**

- No requests are forwarded to the target service.
- Calls return a fallback response or error immediately.

**Half-Open State**

- The breaker allows limited requests to check if the service has recovered.
- If requests succeed, it switches back to Closed. If not, it returns to Open.

!!! info "State Transition Flow"
    ```
    Closed -> (failure threshold reached) -> Open -> (timeout) -> Half-Open -> (success) -> Closed
    ```


## **Use Cases**

- **E-commerce**: Payment gateways failing temporarily switch to fallback payment options.
- **Microservices**: A microservice becomes unresponsive breaker prevents excessive load.
- **API Gateways**: Protect external APIs from crashing under heavy traffic.
- **Circuit Breakers in UI**: Stop retrying user requests that target a failing backend.


## **Implementation**

Several popular libraries and frameworks make circuit breaker implementations simple. Below are code examples in Java and Python.

=== "Java with Resilience4j"

    ```java

    // Resilience4j is a library providing circuit breaker implementations.
    import io.github.resilience4j.circuitbreaker.CircuitBreaker;
    import io.github.resilience4j.circuitbreaker.CircuitBreakerConfig;
    import io.github.resilience4j.circuitbreaker.CircuitBreakerRegistry;

    import java.time.Duration;

    public class Example {
        public static void main(String[] args) {
            // Configuration
            CircuitBreakerConfig config = CircuitBreakerConfig.custom()
                .failureRateThreshold(50)  // Open if 50% of requests fail
                .waitDurationInOpenState(Duration.ofSeconds(5))  // Wait 5 seconds before Half-Open
                .build();

            CircuitBreakerRegistry registry = CircuitBreakerRegistry.of(config);
            CircuitBreaker circuitBreaker = registry.circuitBreaker("myService");

            // Wrap a call in the circuit breaker
            String response = circuitBreaker.executeSupplier(() -> makeHttpRequest());

            System.out.println(response);
        }

        private static String makeHttpRequest() {
            // Simulate an HTTP request here
            return "Success!";
        }
    }
    ```

=== "Python with PyBreaker"

    ```python
    # PyBreaker is a library implementing the Circuit Breaker pattern for Python applications.

    from pybreaker import CircuitBreaker, CircuitBreakerError
    import requests

    # Define a circuit breaker
    breaker = CircuitBreaker(fail_max=3, reset_timeout=5)

    @breaker
    def fetch_data(url):
        response = requests.get(url)
        if response.status_code != 200:
            raise Exception("Service unavailable")
        return response.json()

    try:
        data = fetch_data('https://api.example.com/data')
        print(data)
    except CircuitBreakerError:
        print("Circuit is open. Service unavailable.")
    ```


## **Advanced Topics**

### **Monitoring and Metrics**

Circuit breakers need to be monitored to ensure they perform correctly. You can integrate them with monitoring tools like Prometheus or Grafana. Many libraries offer hooks to capture metrics such as

- Failure rate
- Open/closed/half-open state transitions
- Request success/failure ratios

### **Tuning the Circuit Breaker**

- Failure Threshold Set based on your service’s tolerance.
- Open Timeout Define how long to wait before reattempting.
- Sliding Window Size Determines sensitivity to spikes in failures.

### **Testing Circuit Breakers**

- Chaos Engineering tools like Gremlin or Simian Army.
- Manually disconnect network connections to force failures.
- Use mock services to test the transition between breaker states.


## **Summary**

Circuit breakers are crucial in modern, distributed systems, preventing unnecessary retries and protecting systems from cascading failures. As systems grow in complexity, using the circuit breaker pattern helps maintain high availability and resilience.

---