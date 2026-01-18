# Data State Management in Automated Testing

## The "Seeding as a Service" Pattern

---

## The Core Problem

### "Inconsistent Data = Flaky Tests"

> Regardless of how good your code is, if your data is inconsistent, your tests will fail.

**The "Clean State" Dilemma:**

* **The Pitfall:** Creating data dependencies via the **Test App** itself (e.g., chaining `POST /users` then `POST /orders`).
* **The Risk:** Creates a "Dependency Chain." If an external service (Auth, Payments) fails, your unrelated test fails.
* **The Fix:** **Decouple** test data setup from the application logic.


---


## Definitions: The What & The How

### 1. Data Fixtures ("The What")

* **Definition:** The definition of the fixed state required for a test.
* **Example:** A static JSON file or a factory function defining a "Premium User" (`role: 'admin'`).
* **Purpose:** Ensures a deterministic baseline.

### 2. Data Seeding ("The How")

* **Definition:** The mechanism to populate the database with fixtures.
* **Example:** A script connecting to PostgreSQL to insert rows.
* **Purpose:** Prepares the environment *before* test logic executes.

---

## Where is Seeding Critical?

**Seeding is required where real persistence layers exist:**

1. **Integration Tests:**
* Verifying Lambda interactions with DynamoDB, RDS, or S3.


2. **End-to-End (E2E) & Performance Tests:**
* Simulating full user journeys (Gateway  DB  Gateway).



*Note: Unit tests usually skip seeding in favor of Mocks.*

---

## The Strategy: "The API Sandwich"

In **Playwright**, avoid using the UI to create data (too slow) or direct SQL (bypasses logic).

**The Pattern:**

1. **Fixture Setup:** Use a dedicated API request to **SEED** data.
2. **Test Execution:** Run your actual test logic.
3. **Fixture Teardown:** Use a dedicated API request to **DELETE** data.

---

## Architecture: Seeding as a Service

Since the database is private (inside a VPC), the test runner cannot connect directly. We bridge this gap using an **API Gateway triggering a Lambda**.

**The Flow:**

1. **Playwright:** Generates a UUID (`seed_id`).
2. **Request:** POSTs to `/api/test/seed` with `{ action: 'create', seed_id: '...' }`.
3. **Lambda:** Inserts data into RDS/DynamoDB (Idempotent).
4. **Test:** Runs against the application.
5. **Teardown:** POSTs to `/api/test/seed` with `{ action: 'delete' }`.

---

## Implementation Part 1: The Database (Python)

**Key Concept: Idempotency**
The Lambda uses the `seed_id` to generate deterministic names. If Playwright retries, we don't create duplicates.

```python
def lambda_handler(event, context):
    body = json.loads(event.get('body'))
    seed_id = body.get('seed_id')
    
    # Deterministic Naming
    company_name = f"QA_Vendor_{seed_id}"

    if body['action'] == "create":
        # Check existence first (Idempotency)
        if not exists(company_name):
            insert_company(company_name)
    
    elif body['action'] == "delete":
        delete_company(company_name)

```


---


## Implementation Part 2: The Client (Playwright)

**Best Practice: Use `test.extend` (Fixtures)**

* **Lazy-Loaded:** Only runs if the test requests it.
* **Composable:** One fixture can depend on another.
* **Automatic Teardown:** Uses the `yield` pattern.

```typescript
// fixtures.ts
export const test = base.extend<{ seedCatalog: SeededData }>({
  seedCatalog: async ({ request }, use) => {
    const seed_id = faker.string.uuid();
    
    // 1. SETUP (Call Lambda)
    await request.post(ENDPOINT, { data: { action: 'create', seed_id } });

    // 2. YIELD (Run Test)
    await use({ seed_id, ...data });

    // 3. TEARDOWN (Call Lambda)
    await request.post(ENDPOINT, { data: { action: 'delete', seed_id } });
  },
});
```


---


## The Test Execution

The test remains clean and readable. It requests the `seedCatalog` fixture, which handles all the complexity behind the scenes.

```typescript
import { test } from './fixtures';

test('Search API finds the seeded part', async ({ request, seedCatalog }) => {
  // Setup is already done! We have our data:
  const { part_number } = seedCatalog;

  // Execute Public API
  const response = await request.get(`/api/v1/search?q=${part_number}`);
  
  // Verify
  expect(response.ok()).toBeTruthy();
});

```

---

## Summary Matrix: When to Seed?

| Test Type | Persistence Layer | Strategy |
| --- | --- | --- |
| **Unit Test** | None (In-memory) | **Mock/Stub.** (No seeding). |
| **Integration** | Real (Docker/Dev DB) | **Seed.** Insert specific rows needed. |
| **E2E / System** | Real (Staging DB) | **Seed.** Reset/Prep DB for clean baseline. |
| **Load Test** | Real (Prod Clone) | **Massive Seed.** Stress-test queries. |

---

## Key Takeaways

1. **Decouple:** Don't use your App UI to build test data.
2. **Sandwich:** Setup -> Test -> Teardown.
3. **Idempotency:** Ensure your seeder can run multiple times without error.
4. **Playwright Fixtures:** Use `test.extend` over `beforeEach` for better control and automatic cleanup.