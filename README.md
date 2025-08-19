# Goldbach
Use AWS Compute to search for anti-proofs of the Goldbach Conjecture.
 Goldbach Conjecture  Empirical Verifier (AWS Batch, NDJSON to S3)

This repository contains an AWSfirst, reproducible pipeline to empirically test the (strong) Goldbach Conjecture — that every even integer > 2 is the sum of two primes — and to stream results for every even number in a range to Amazon S3 as NDJSON.

> Empirical checks cannot prove Goldbach (infinitely many evens remain). They can disprove it by finding a single counterexample (“anti‑proof”).

Caveats
There are other more optimized distributed computer models that could do this task more efficiently.
If looking for antiproofs past the work of Oliveira, the costs to calculate these primes are extreme.  This is largely just a demonstration project.  Only significant funding could continue the additional search for an antiproof.

 Background

 Statement. Every even integer greater than 2 is the sum of two primes.  
  See Goldbach’s Conjecture (overview, history).  
  https://en.wikipedia.org/wiki/Goldbach%27s_conjecture

 Known computational checks. A landmark distributed computation by Tomás Oliveira e Silva, Siegfried Herzog, and Silvio Pardi verified the conjecture for all even n ≤ 4×10¹⁸ (2013/2014) and published the methods/results:  
  Project page: https://sweet.ua.pt/tos/goldbach.html  
  Paper (Math. of Computation, 2014): https://www.ams.org/mcom/201483288/S002557182013027871/S002557182013027871.pdf

 Related results.
   Chen’s Theorem: Every sufficiently large even number is the sum of a prime and a semiprime (1973).  
    https://en.wikipedia.org/wiki/Chen%27s_theorem
   Weak Goldbach (odd ≥ 7 is the sum of three primes) has a claimed proof (H. Helfgott, 2013; long‑form write‑ups and follow‑up).  
    https://en.wikipedia.org/wiki/Goldbach%27s_weak_conjecture

 “Anti‑proof” idea (counterexample search)
A single even number not representable as a sum of two primes would disprove the conjecture. This project does a systematic search over ranges of even numbers and records the result for every tested even integer as a streaming NDJSON file.



 What this repo deploys

 ECR repository for the worker image.
 CodeBuild project that embeds the worker code (no external ZIP) and builds/pushes `:latest` to ECR.
 AWS Batch (managed SPOT) compute environment, job queue, and job definition.
 S3 results bucket `goldbachsummaries<account><region>` for NDJSON output.

The worker:
 generates a prime table (sieve) up to `RANGE_END`
 iterates even n in `[RANGE_START, RANGE_END]`
 finds a Goldbach pair `(p1, p2)` if it exists
 streams one JSON object per line to S3 (multipart upload)
  ```json
  {"n": 4, "passes": true, "p1": 2, "p2": 2}
  {"n": 6, "passes": true, "p1": 3, "p2": 3}
  …
