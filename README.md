# Abigail Ray || ECON 5200 || Diagnosing Experimental Failures in A/B Testing

## Objective
I ran and debugged A/B testing methods on simulated data to see how CUPED, sample ratio checks, marketplace interference, Bayesian testing and peeking can each change what an experiment seems to show.

## Methodology
- Found and fixed three bugs in an analyst's CUPED code (the theta formula, centering the covariate, and the standard error), then saved a corrected version as `src/cuped_utils.py`.
- Ran chi-squared sample ratio mismatch tests on two experiments with different sizes and imbalances.
- Simulated a marketplace with limited inventory in three worlds (no interference, individual randomization with interference, and a global rollout) and measured the bias of the naive ATE against two benchmarks.
- Ran a Beta-Binomial Bayesian A/B test with Beta(1, 1) priors, computed P(B > A) and expected loss, applied a ship rule, and compared the result with a permutation test.
- Wrote my own `bayes_ab()` function and used it on a test with four times the users.
- Simulated 1,000 experiments with no true effect, peeking at 50 points each, and compared uncorrected peeking, a Bonferroni correction, and a single test at the end.

## Key Findings
- The analyst's CUPED code reported a 0.0% variance reduction. After my fixes it was 82.7%, with the SE falling from 0.1539 to 0.0641 and the CUPED ATE at 2.0185 against a true 2.0. Theta changed from 0.9013 to 0.6965.
- A 0.6 pp imbalance among 100,000 users gave chi-squared 14.40, and a 0.05 pp imbalance among 20 million users gave 20.00. Both tripped the p < 0.001 alarm, but the larger statistic came from the larger sample, not the larger imbalance. I treated the 0.6 pp case as the real concern.
- In the marketplace, the naive ATE was 0.1861, against 0.1428 for rolling the discount out to everyone. That is an overstatement of 23.3% of the naive estimate (19.4% against the true individual effect of 0.15). The control arm's purchase rate fell by 0.1182 and the treated arm's by 0.0818, which widened the gap.
- In the Bayesian test, P(B > A) was 0.8866 and the expected loss of shipping B was 0.000473, so the ship rule said False. The permutation test gave p = 0.2302.
- With four times the users, my `bayes_ab()` gave P(B > A) = 0.9951 and an expected loss of 0.000007, so the rule said to ship B.
- With no true effect, uncorrected peeking crossed p < 0.05 at least once in 32.4% of experiments. Bonferroni gave 1.9%, and a single test at the end gave 5.3%.
