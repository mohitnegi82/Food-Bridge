# Food Bridge Security Specification

## Data Invariants
1. A user cannot create a `FoodPost` for another restaurant.
2. An NGO cannot request their own food post.
3. A `Request` status can only be changed to `accepted`/`rejected` by the restaurant that owns the food post.
4. A `Request` status can only be changed to `collected`/`delivered` by the NGO that made the request (after acceptance).
5. All IDs must be strictly validated.
6. Timestamps must be verified against server time.

## The Dirty Dozen Payloads

1. **Identity Spoof**: User A tries to create a user profile for User B.
2. **Role Escalation**: User registers as `admin`. (Should be restricted to enum).
3. **Ghost Posts**: User A creates a food post with User B's `restaurantId`.
4. **Invalid Date**: Creating a post with an expiry time in the past (Actually rules don't strictly block past dates usually without complex logic, but we enforce `timestamp` type).
5. **Unauthorized Acceptance**: NGO tries to mark their own request as `accepted`.
6. **State Skip**: NGO tries to mark a `pending` request as `delivered` without it being `accepted`.
7. **Resource Poisoning**: Extremely long string for `foodName`.
8. **Shadow Field**: Adding `isVerified: true` to a request.
9. **Orphaned Request**: Requesting a non-existent `foodPostId`.
10. **Duplicate Request**: Requesting a post that is already `completed`. (Logic level).
11. **Email Spoof**: Creating a verified-only resource with an unverified email token.
12. **Blanket Query**: Unauthenticated user trying to list all users.

## Test Runner (Conceptual)

`firestore.rules.test.ts` (Mockup for reasoning):
- `test('unauthenticated users cannot read/write anything')`
- `test('users can only write their own profile')`
- `test('restaurants can only update their own posts')`
- `test('only owners can update request status')`
- `test('hasOnly() blocks unexpected fields')`

I'll proceed to implement the final `firestore.rules` and the React application.
Building the React application involves:
1. `firebase.ts`: Firebase initialization.
2. `App.tsx`: Routing and Layout.
3. `components/Auth.tsx`: Login/Signup.
4. `components/RestaurantDashboard.tsx`: Add food, list food, incoming requests.
5. `components/NgoDashboard.tsx`: Available food, request meal, request status.
6. `components/Navbar.tsx`: Navigation.
