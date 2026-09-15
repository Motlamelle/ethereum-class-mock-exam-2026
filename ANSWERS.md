# Written section, mock

Answer all five questions. **Maximum 120 words each.**

---

## Question 1 (5 marks)

State your `poolId` and list the exact values that produced it. Then explain what would have
happened if you had deployed `Task3Liquidity` with a tick spacing of 60 instead of 200, everything
else unchanged. Say what `poolId` would have done, and what the first failure would have been.

**Answer:**
What to include
State your poolId (bytes32 hash from Task 2)

List the 5 exact inputs that produced it: currency0 address, currency1 address, fee (10000), tickSpacing (200), and hooks (address(0)).
Explain changing tickSpacing to 60 : Changing tickSpacing produces a completely different poolId because tickSpacing is part of the PoolKey struct.
State the first failure: The call reverts with "the pool is not open, run Task 2 first..." (or fails poolExists()) because no pool was initialized on PoolManager with tickSpacing = 60

---

## Question 2 (5 marks)

You put in 5 whole tokens of currency1. State which of your two tokens became currency0 and how you
knew, the output you predicted, the output you actually received, and the arithmetic that got you
from 5 to your prediction. Then split the difference between prediction and actual into the part
that is fee and the part that is not, with numbers.

**Answer:**

State currency0 & how you knew : Name which token became currency0 (TUT or CAFE) based on your alphaIsCurrency0 call (true means Token A sorted lower alphanumerically).
State predicted vs. actual output: Record both exact figures in 18-decimal wei units.
Show the arithmetic:
Start with 5 tokens of currency1 (5000000000000000000).
Subtract the 1% fee (_fee = 10000), leaving 4.95 tokens (4950000000000000000).
Apply the exchange rate of 16 (divide by 16 if swapping CAFE to TUT, or multiply by 16 if swapping TUT to CAFE).


---

## Question 3 (5 marks)

`Task3Liquidity` has to be holding your tokens and it also has to have approved the liquidity
router. Explain why both are needed and what each one does. Then call `addLiquidity` from a freshly
deployed `Task3Liquidity` that you have not sent any tokens to, quote the error message exactly,
and say which of the two requirements it was complaining about.

**Answer:**

Explain why holding tokens is needed: Task3Liquidity pays for the liquidity position directly, so tokens must physically reside in its contract balance.
Explain why approval is needed: ERC-20 security requires Task3Liquidity to explicitly grant the liquidityRouter permission (approve) to pull tokens from its balance.
Deliberate Error Test: Deploy a fresh unfunded Task3Liquidity and call addLiquidity.
Quote the exact error & requirement: Quote the exact revert message from the transaction log (e.g., FAIL / ERC-20 transfer failure) and state that it complained about insufficient contract token balance (not approval).


---

## Question 4 (5 marks)

State your live tick and the range you chose, and say why you chose it. Then answer this: if you
had chosen a range sitting entirely **below** the live tick, what would have happened? Name which
of your two tokens the pool would have taken, which it would have left untouched, and why that is
the way round it is.

**Answer:**

State live tick & range: State your currentTick, tickLower, and tickUpper.
Why chosen: Explain that you selected a range centered around the live tick with bounds aligned to integer multiples of tickSpacing (200) to remain active.
Range entirely BELOW live tick:The pool takes 100% currency1 and leaves currency0 completely untouched (0 amount).
Why: In concentrated liquidity, as current price or tick rises above a range, the range is fully sold into the higher-value asset (currency1).

---

## Question 5 (5 marks)

State the number `startingSqrtPriceX96` returned for your run. Show how that number relates to your
starting price of 16, or one sixteenth, and to two to the power of ninety six. Then explain why the
protocol stores the square root of the price rather than the price itself, and why your tick came
out at roughly plus or minus 27727.

**Answer:**

Why store square root of price: Uniswap liquidity invariant formulas use $\sqrt{P}$ directly; storing $\sqrt{P}$ avoids expensive on-chain square root operations during every swap.
Why tick is roughly $27727$: Using $\text{price} = 1.0001^{\text{tick}}$, taking natural logarithms with P = 16 gives tick that is +/- 27727

---
