# Coupon Test Design

## Reusable Prompt Template

Act as a Senior QA Engineer specializing in risk-based testing.
Analyze this feature:

User story:
As a customer, I want to apply a coupon code during checkout so that I can receive a discount on my purchase.

Requirements:
- Coupon code must be valid.
- Coupon must not be expired.
- Coupon must meet minimum purchase requirements.
- Discount must be calculated correctly.
- Coupon can be removed.
- Invalid coupons must show an error.

Confirmed business rules:
- Minimum purchase: $50.
- Maximum discount: $20.
- Coupon discount: 10%.
- Orders below $50 cannot use the coupon.

Generate:
1. Requirement analysis
2. Clarification questions
3. Positive test scenarios
4. Negative test scenarios
5. Boundary value analysis
6. Equivalence partitioning
7. Business rule validation
8. Security testing
9. Concurrency testing
10. API validation
11. UI behavior testing
12. Regression testing
13. Risk matrix
14. Automation candidates

For each test case include:
- Test ID
- Scenario
- Preconditions
- Test data
- Steps
- Expected result
- Risk level
- Automation suitability

Do not invent business rules.
Clearly identify missing requirements.
Prioritize high-risk scenarios.
Review output for duplicates and gaps.

---

# Requirement Analysis

## User Story
As a customer, I want to apply a coupon code during checkout so that I can receive a discount on my purchase.

## Functional Requirements
1. Coupon must be valid.
2. Coupon must not be expired.
3. Minimum order value is $50.
4. Discount rate is 10%.
5. Maximum discount is capped at $20.
6. Invalid coupons must display an error.
7. Coupon can be removed.

## Discount Formula
Discount = min(Order Amount × 10%, $20)

## Boundary Rules
| Order Amount | Expected Result |
|--------------|----------------|
| $49.99 | Reject coupon |
| $50.00 | Accept coupon |
| $50.01 | Accept coupon |
| $200.00 | $20 discount |
| $300.00 | $20 discount (cap applied) |

---

# Clarification Questions

1. Are coupon codes case-sensitive?
2. Is coupon usage limited per user?
3. Are guest users allowed to apply coupons?
4. What rounding rules apply to discounts?
5. Are taxes calculated before or after discount?
6. Are shipping charges included in the minimum purchase calculation?
7. What is the exact coupon expiration timestamp behavior?
8. Can multiple coupons be combined?
9. Are certain products excluded?
10. Should applied coupons be revalidated at order submission?

---

# Key Test Scenarios

## Positive
- Apply valid coupon on $50 order.
- Apply valid coupon on $100 order.
- Apply valid coupon on $300 order and verify cap.
- Remove applied coupon.

## Negative
- Invalid coupon.
- Expired coupon.
- Empty coupon.
- Order value $49.99.
- Tampered API request.

---

# Boundary Values

| Scenario | Value | Expected |
|-----------|---------|----------|
| Below minimum | $49.99 | Reject |
| At minimum | $50.00 | Accept |
| Above minimum | $50.01 | Accept |
| Just below cap threshold | $199.99 | Discount below or equal to cap according to rounding rule |
| At cap threshold | $200.00 | $20 discount |
| Above cap threshold | $200.01 | $20 discount |

---

# Risk Matrix

| Area | Impact | Likelihood | Risk |
|--------|--------|------------|------|
| Discount calculation | High | High | Critical |
| Minimum purchase validation | High | High | Critical |
| Expiration validation | High | Medium | High |
| API manipulation | High | High | Critical |
| Coupon removal | Medium | Medium | Medium |
| UI messaging | Low | Medium | Low |

---

# Automation Candidates

## Priority P0
- Discount calculation validation
- Minimum purchase validation
- Maximum discount cap validation
- API validation
- Invalid coupon handling

## Suggested Playwright Test

```ts
test('applies valid coupon', async ({ page }) => {
  await page.goto('/checkout');

  await page.getByLabel('Coupon code').fill('SAVE10');

  await page.getByRole('button', {
    name: 'Apply coupon'
  }).click();

  await expect(
    page.getByText('Coupon applied')
  ).toBeVisible();
});
```
