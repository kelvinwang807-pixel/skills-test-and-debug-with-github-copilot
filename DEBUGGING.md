# Debugging notes

## What the code does

split_bill takes a subtotal, number of people, and optional tip percentage. The data flows through these steps in bill_splitter.py:11:

It rejects people unless it’s a positive integer; booleans are rejected too.
It converts the subtotal and tip percentage to Decimal via str(...), then rejects negative values.
It calculates the tip and adds it to the subtotal, rounding the total to cents using ROUND_HALF_UP.
It divides that rounded total by the number of people, then rounds each share to cents with the same rule.
It returns a list containing that share once per person.
One important consequence: it rou

## Baseline evidence

Returns one Decimal("0.00") share per person when the subtotal is zero, as tested in test_bill_splitter.py:8.
Raises ValueError when people is zero, negative, non-integer, or True, as tested in test_bill_splitter.py:12.
Raises ValueError for a negative subtotal or negative tip percentage, as tested in

## Hypothesis

A likely cause is that the code rounds one equal share to cents, then repeats that same rounded amount for everyone. That can make the shares’ sum differ from the rounded bill total.

For example, $0.03 ÷ 2 rounds each share to $0.02, so the shares total $0.04: a cent is invented. With $0.04 ÷ 3, each share rounds to $0.01, totaling $0.03: a cent is lost. The remainder needs to be distributed among shares instead of rounding one share and repeating it.
