# 🐦 Polly

---

### Piping timeout with retries

- A resiliency pipeline with timeout being defined first before a retry and a resiliency pipeline with the retry being defined first before the timeout have different behaviors.
    - The resilience pipeline where the retry is defined first before the timeout behaves such that the timeout is dependent on the retry attempts.
        - Each retry attempt will follow the timeout defined.
        - A timeout exception will be returned if the retry attempt fully uses the timeout that was set.
    - The resilience pipeline where the timeout is defined first before the retry will behave in that the entire retry mechanism shall run under the defined timeout.
        - If the retry mechanism fails to finish within the set timeout, a timeout exception will be returned.